# Csharp12 new features summary

C# 12 includes the following new features. You can try these features using the latest Visual Studio 2022 version or the .NET 8 SDK.

**Primary constructors** - Introduced in Visual Studio 2022 version 17.6 Preview 2.

**Collection expressions** - Introduced in Visual Studio 2022 version 17.7 Preview 5.

**Inline arrays** - Introduced in Visual Studio 2022 version 17.7 Preview 3.

**Optional parameters in lambda expressions** - Introduced in Visual Studio 2022 version 17.5 Preview 2.

**ref readonly parameters** - Introduced in Visual Studio 2022 version 17.8 Preview 2.

**Alias any type** - Introduced in Visual Studio 2022 version 17.6 Preview 3.

**Experimental attribute** - Introduced in Visual Studio 2022 version 17.7 Preview 3.

**Interceptors** - Preview feature Introduced in Visual Studio 2022 version 17.7 Preview 3.

## Primary constructors

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



## The terse syntax to work with collections

## Anonymous function parameters by default

## Alias for any type

## The nameof refinement

## Inline arrays

## The code interception

## Conclusion
