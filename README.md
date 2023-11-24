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



## The terse syntax to work with collections

## Anonymous function parameters by default

## Alias for any type

## The nameof refinement

## Inline arrays

## The code interception

## Conclusion
