# Csharp12 new features summary

C# 12 includes the following new features. You can try these features using the latest Visual Studio 2022 version or the .NET 8 SDK.

**1. Primary constructors** - Introduced in Visual Studio 2022 version 17.6 Preview 2.

**2. Collection expressions** - Introduced in Visual Studio 2022 version 17.7 Preview 5.

**3. Inline arrays** - Introduced in Visual Studio 2022 version 17.7 Preview 3.

**4. Optional parameters in lambda expressions** - Introduced in Visual Studio 2022 version 17.5 Preview 2.

**5. ref readonly parameters** - Introduced in Visual Studio 2022 version 17.8 Preview 2.

**6. Alias any type** - Introduced in Visual Studio 2022 version 17.6 Preview 3.

**7. Experimental attribute** - Introduced in Visual Studio 2022 version 17.7 Preview 3.

**8. Interceptors** - Preview feature Introduced in Visual Studio 2022 version 17.7 Preview 3.

## 1. Primary constructors

We can create constructor in the class declaration.

```csharp
class Point(int posX, int posY)
{
    private int X = posX;
    private int Y = posY;

    public bool IsInArea(int minX, int maxX, int minY, int maxY)
        => X <= maxX && X >= minX && Y <= maxY && Y >= minY;
}
// ....
var point = new Point(100, 50);
Console.WriteLine(point.IsInArea(30, 150, 50, 150)); // True
```

When we add another constructor, we should also add this(....):

```csharp
class Point(int posX, int posY)
{
    private int X = posX;
    private int Y = posY;
    private Color color;

    public Point(int posX, int posY, Color color) : this(posX, posY)
    {
        this.color = color;
    }

    // ....
}
```

###  Primary constructors with Dependency Injection

When we use a standard library, the dependency injection syntax may not be so large.

Instead of several reps of the same thing:

```csharp
public class AuthorizeService
{
    private readonly UserRepository _users;
    private readonly PasswordHasher<User> _hasher;

    public AuthorizeService(UserRepository repository,
                            PasswordHasher<User> hasher)
    {
        _users = repository;
        _hasher = hasher;
    }

    // ....
}
```

We can make the code more concise:

```csharp
public class AuthorizeService(UserRepository repository, 
                              PasswordHasher<User> hasher)
{
    private readonly UserRepository _users = repository;
    private readonly PasswordHasher<User> _hasher = hasher;

    // ....
}
```

Another sample for Primary constructor with Dependency injection

```csharp
public interface IService
{
    Distance GetDistance();
}

public class ExampleController(IService service) : ControllerBase
{
    [HttpGet]
    public ActionResult<Distance> Get()
    {
        return service.GetDistance();
    }
}
```

### Initialize property from primary constructor parameters

The following code initializes two readonly properties that are computed from primary constructor parameters:

```csharp
public readonly struct Distance(double dx, double dy)
{
    public readonly double Magnitude { get; } = Math.Sqrt(dx * dx + dy * dy);
    public readonly double Direction { get; } = Math.Atan2(dy, dx);
}
```

You can also use primary constructors when the properties aren't readonly.

```csharp
public struct Distance(double dx, double dy)
{
    public readonly double Magnitude => Math.Sqrt(dx * dx + dy * dy);
    public readonly double Direction => Math.Atan2(dy, dx);

    public void Translate(double deltaX, double deltaY)
    {
        dx += deltaX;
        dy += deltaY;
    }

    public Distance() : this(0,0) { }
}
```

### Invoke a Base Class Primary Constructor from the Derived Class Primary Constructor

The **Base Class** would look something like the following code:

```csharp
public class BankAccount(string accountID, string owner)
{
    public string AccountID { get; } = ValidAccountNumber(accountID) 
        ? accountID 
        : throw new ArgumentException("Invalid account number", nameof(accountID));

    public string Owner { get; } = string.IsNullOrWhiteSpace(owner) 
        ? throw new ArgumentException("Owner name cannot be empty", nameof(owner)) 
        : owner;

    public override string ToString() => $"Account ID: {AccountID}, Owner: {Owner}";

    public static bool ValidAccountNumber(string accountID) => 
    accountID?.Length == 10 && accountID.All(c => char.IsDigit(c));
}
```

One derived class would present a checking account:

```csharp
public class CheckingAccount(string accountID, string owner, decimal overdraftLimit = 0) : BankAccount(accountID, owner)
{
    public decimal CurrentBalance { get; private set; } = 0;

    public void Deposit(decimal amount)
    {
        if (amount < 0)
        {
            throw new ArgumentOutOfRangeException(nameof(amount), "Deposit amount must be positive");
        }
        CurrentBalance += amount;
    }

    public void Withdrawal(decimal amount)
    {
        if (amount < 0)
        {
            throw new ArgumentOutOfRangeException(nameof(amount), "Withdrawal amount must be positive");
        }
        if (CurrentBalance - amount < -overdraftLimit)
        {
            throw new InvalidOperationException("Insufficient funds for withdrawal");
        }
        CurrentBalance -= amount;
    }
    
    public override string ToString() => $"Account ID: {AccountID}, Owner: {Owner}, Balance: {CurrentBalance}";
}
```

There's one potential concern with class hierarchies and primary constructors: it's possible to create multiple copies of a primary constructor parameter as it's used in both derived and base classes. 

The following code example creates two copies each of the owner and accountID field:

```csharp 
public class SavingsAccount(string accountID, string owner, decimal interestRate) : BankAccount(accountID, owner)
{
    public SavingsAccount() : this("default", "default", 0.01m) { }
    public decimal CurrentBalance { get; private set; } = 0;

    public void Deposit(decimal amount)
    {
        if (amount < 0)
        {
            throw new ArgumentOutOfRangeException(nameof(amount), "Deposit amount must be positive");
        }
        CurrentBalance += amount;
    }

    public void Withdrawal(decimal amount)
    {
        if (amount < 0)
        {
            throw new ArgumentOutOfRangeException(nameof(amount), "Withdrawal amount must be positive");
        }
        if (CurrentBalance - amount < 0)
        {
            throw new InvalidOperationException("Insufficient funds for withdrawal");
        }
        CurrentBalance -= amount;
    }

    public void ApplyInterest()
    {
        CurrentBalance *= 1 + interestRate;
    }

    public override string ToString() => $"Account ID: {accountID}, Owner: {owner}, Balance: {CurrentBalance}";
}
```


## 
