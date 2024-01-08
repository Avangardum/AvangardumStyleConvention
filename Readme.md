# Avangardum's style convention

This is the convention for my personal projects, covering various aspects of development.

## Style convention versioning

Current version: 5.0.1

Each project using this convention should have a file named StyleConvention.md in its root directory, containing the
name, a used version, a link to a corresponding version branch of this repository and the full text of this convention, 
like this:

```markdown
This project uses Avangardum's style convention v1.0

https://github.com/Avangardum/AvangardumStyleConvention/tree/version/1.0

# Local rules (take precedence over the base convention)

local rules

# Avangardum's style convention v1.0

this convention
```

Any project specific rules should be written in this file.

Version has the following format: major.minor[.patch]

Major version is incremented when there are breaking changes.

Minor version is incremented when there are non-breaking changes.

Patch version is incremented when there are fixes that don't change the essence of the convention.

## C#

### Naming
| Object                                     | Naming              |
|--------------------------------------------|---------------------|
| Namespace                                  | `SomeNamespace`     |
| Non-interface type                         | `SomeType`          |
| Interface                                  | `ISomeInterface`    |
| Non-local constant / static readonly field | `SomeConstant`      |
| Private field                              | `_someField`        |
| Non-private field                          | `SomeField`         |
| Event                                      | `SomeEvent`         |
| Property                                   | `SomeProperty`      |
| Function that doesn't return Task          | `SomeFunction`      |
| Function that returns Task                 | `SomeFunctionAsync` |
| Function parameter                         | `someParameter`     |
| Local variable                             | `someVariable`      |
| Local constant                             | `someConstant`      |
| Region                                     | `SomeRegion`        |
| Goto label                                 | `SomeGotoLabel`     |
| Tuple / anonymous type member              | `SomeMember`        |

### Comments

Comments should be written in English.

Comments should be written using `//`, not `/* */`.

`//` should always be followed by a whitespace.

If `//` is not in the beginning of a line, it should also be preceded by a whitespace.

### Namespaces

Root namespace for the project should follow the following pattern: *Developer/Company*.*Project* 
(examples: `Avangardum.TwilightRun`, `Hyperbox.DroneTanksArena`).

Use file-scoped namespaces where possible.

### Types

Each source code file should contain only one non-nested type.

Do not use primary constructors.

### Enums

Enums shouldn't have a `None` value. For the purpose of representing nothing use `null`.

All enum constants should NOT have an integer value explicitly specified.

<details>
<summary>Example</summary>

```csharp
// incorrect
public enum Season { None = 0, Winter = 1, Spring = 2, Summer = 3, Autumn = 4 }

// correct
public enum Season 
{ 
    Winter, 
    Spring, 
    Summer, 
    Autumn,
}
```
</details>

### Member declaration order

- Nested types
- Constants and static readonly fields
- Fields
- Constructors
- Destructors
- Events
- Properties
- Indexers
- Methods

Within each of these groups order by access:

- public
- protected internal
- internal
- protected
- private protected
- private

Within each of the access groups, order by static, then non-static:

- static
- non-static

Within each of the static/non-static groups of fields, order by readonly, then non-readonly:

- readonly
- non-readonly

### Events

Events should use the `EventHandler` delegate. If there are no arguments to pass, `EventArgs.Empty` should be passed. 
If there are arguments to pass, the generic version of `EventHandler` should be used and arguments should be passed 
in an event arguments object.

Event argument types should follow one of the following patterns: *event* + Args (example: `DyingArgs`),
*sender* + *event* + Args (example: `CharacterDyingArgs`).

Event handler method names should follow one of the following patterns: On + *event* (example: `OnDying`), 
On + *sender* + *event* (example: `OnCharacterDying`).

Event handler methods should have 2 parameters: `sender` and `e`.

Events should not be initialized with an empty delegate.

Events should be invoked using the `Invoke` method.

Events should be invoked using the `?.` operator.

Events that are invoked before something happens should be named as verbs in present continuous 
(example: `CharacterDying`).

Events that are invoked after something happens should be named as verbs in past simple. (example: `CharacterDied`).

<details>
<summary>Example</summary>

```csharp
// incorrect
public class Character
{
    public event Action<Character, Character> Death = delegate {};

    public string Name { get; set; }
    
    private void Die(Charater killer)
    {
        Death(this, killer);
    }
}

public class KillReporter()
{
    private void OnKill(Character victim, Character killer)
    {
        _gameLogger.Write($"{killer.Name} killed {victim.Name}");
    }
}


// correct
public class Character
{
    public record DiedEventArgs(Character killer);

    public event EventHandler<DiedEventArgs>? Died;

    public string Name { get; set; }
    
    private void Die(Character killer)
    {
        Died?.Invoke(this, new DiedEventArgs(killer));
    }
}

public class KillReporter()
{
    private void OnCharacterDied(object sender, Character.DiedEventArgs e)
    {
        var victim = (Character)sender;
        _gameLogger.Write($"{e.killer.Name} killed {victim.Name}");
    }
}
```
</details>

### Functions, properties, indexers

Do not use expression body declaration if it is more than one line long. Having a signature and an expression body on
different lines is still acceptable.

Do not mutate parameters unless they are `ref` or `out`.

Add the `m_` prefix to local variables that are meant to be mutated. If a local variable doesn't have this prefix, it
should not be mutated.

Do not use the `delegate` keyword for declaring anonymous functions. Use lambda functions instead.

Lambda function parameters should be named either as normal function parameters, or as abbreviations.

<details>
<summary>Example</summary>

```csharp
// incorrect
private List<User> GetTargetUsers() => _users
    .Where(delegate(User x) { return x.IsVerified; })
    .Where(delegate(User x) { return x.IsOnline; })
    .ToList();


// correct
private List<User> GetTargetUsers()
{
    return _users
        .Where(u => u.IsVerified)
        .Where(u => u.IsOnline)
        .ToList();
}
```
</details>

Local function parameters and variables should have the `_` suffix. For local function inside another local function
the suffix should be `__` and so on.

<details>
<summary>Example</summary>

```csharp
public void Foo()
{
    var a = 1;
    Bar(a);
    
    void Bar(int a_)
    {
        var b_ = 2;
        Qux(a_, b_);
        
        void Qux(int a__, int b__)
        {
            var c__ = 3;
        }
    }
}
```
</details>

If possible, return Task from a method instead of making the method async and awaiting the task.

<details>
<summary>Example</summary>

```csharp
// incorrect
async Task FooAsync() => await BarAsync();


// correct
Task FooAsync() => BarAsync();
```
</details>

### Interfaces

Standard names for interfaces:

| Interface purpose                                                                         | Naming                           |
|-------------------------------------------------------------------------------------------|----------------------------------|
| Allows to create something                                                                | ISomethingFactory                |
| Allows to get something (not necessarily creating it)                                     | ISomethingGetter                 |
| Allows to set something                                                                   | ISomethingSetter                 |
| Allows to get or set something                                                            | ISomethingRepository             |
| Notifies about something (with an event, or otherwise)                                    | ISomethingPublisher              |
| Maps some one thing to some another thing (both can be named with a shared name)          | ISomethingMapper                 |
| Maps one thing to another item (both can NOT be named with a shared name)                 | IOneThingToAnotherItemMapper     |
| Maps one thing to another item (both can NOT be named with a shared name) bidirectionally | IOneThingToFromAnotherItemMapper |

### Collections

Use collection expressions where possible.

Use immutable collections (`ImmutableList`, `ImmutableHashSet`, etc.) where possible.

Accept the most abstract collection types (`IEnumerable` > `IReadOnlyCollection` > `IReadonlyList`, etc.) as parameters.

Return the most concrete collection types.

### Formatting

There should be no whitespace between a method name and its parameters.

There should be a single whitespace between a loop or branching operator and its condition.

If there is a block of code in parentheses, square brackets or curly braces, spanning multiple lines,
it should be either be like a single line, but with line breaks preventing overflowing the line width limit or with 
each element and opening/closing symbol on its own line.

Indentation: 4 spaces.

Having a single newline between members of a type is always acceptable. However, it is possible to omit it if all of
the following conditions are met:
- Both members have the same member declaration order (for example, both are private non-static non-readonly fields).
- Both members occupy only 1 line each (including comments and initializers).

<details>
<summary>Example</summary>

```csharp
// incorrect
private int _a;

/// <summary>
/// Lorem ipsum
/// </summary>
private int _b;
private int[] _c = [
    1,
    2,]
public int C { get; set; }

public int SqrC => A * A;
public int GetCPlus (int n) => C + n;

public int GetCMinus (int n) => C - n;
public int GetFoo (int n) {
    if(n > 0) return _a;
    else return _b;
}


// correct
private int _a;

/// <summary>
/// Lorem ipsum
/// </summary>
private int _b;

private int[] _c = 
[
    1,
    2,
];

public int C { get; set; }
public int SqrC => A * A;

public int GetCPlus(int n) => C + n;
public int GetCMinus(int n) => C - n;

public int GetFoo(int n)
{
    if (n > 0) return _a;
    else return _b;
}
```
</details>

### Miscellaneous

When comparing a value to with boundary values, all of the values should be sorted in ascending order from left to right.

Use var where possible. (Unless you need to specify the collection type to use a collection expression, in such case
prefer specifying the exact type and using a collection expression over using var and a collection initializer.)

Use explicit privacy modifiers where possible. The exception are interface members, they should be implicitly public.

Use trailing commas when each element is in a separate line. Don't use them when all elements are in the same line.

<details>
<summary>Example</summary>

```csharp
// incorrect
void Foo(int value) // method
{
    var numbers1 = new List<int> { 1, 2, 3, }; 
    var numbers2 = new List<int>
    {
        1,
        2
    };
    bool isValid = value >= MinValue && value <= MaxValue;
    if (!isValid) return;
    Bar();
}


// correct
private void Foo(int value) // method
{
    List<int> numbers1 = [1, 2, 3];
    List<int> numbers2 = 
    [
        1,
        2,
    ];
    var isValid = MinValue <= value && value <= MaxValue;
    if (!isValid) return;
    Bar();
}
```
</details>

When using a switch statement or expression, if the default case is invalid,
it should throw an ArgumentOutOfRangeException with no parameters.

## Files

| Object | Naming     |
|--------|------------|
| File   | `SomeFile` |

## Unity

| Object                                                  | Naming       |
|---------------------------------------------------------|--------------|
| All Unity objects (Game objects, Animator states, etc.) | `SomeObject` |

## Zenject

| Object           | Naming            |
|------------------|-------------------|
| Binding ID       | `SomeBinding`     |
| Injection Method | `SomeClassInject` |

Injection methods priority (from most recommended to least recommended):
- Injection using constructor
- Injection using an injection method
- Injection using injectable fields/properties

A class should have no more than one injection method.

## NUnit

| Object          | Naming                                                       |
|-----------------|--------------------------------------------------------------|
| Test fixture    | `SomeClassTests`                                             |
| Test            | `SomeMethod_SomeStateAndArgs_SomeResult` (no `Async` suffix) |

Each test fixture should be either abstract or sealed.

In sealed fixtures, setup and teardown methods should be named `SetUp` and `TearDown`. In abstract fixtures, their names
should be prefixed with the class name, like `TestsBaseSetUp`.

Use the constraint model for assertions where possible.

When asserting that an object, inheriting from UnityEngine.Object, is null or not null, use `UnityEngine.Assertions.Assert`
instead of `NUnit.Framework.Assert`. For this purpose use the following type alias: `using UAssert = UnityEngine.Assertions.Assert;`

A test fixture should have no more than one setup and teardown methods.

<details>
<summary>Example</summary>

```csharp
// incorrect
Assert.That(result != 0);
Assert.AreNotEqual(0, result);

// correct
Assert.That(result, Is.Not.EqualTo(0));
```
</details>

## Microsoft.Extensions.DependencyInjection

For registering dependencies, use extension methods for IServiceCollection, each method containing a focused group of
registrations, and returning IServiceCollection.

Use method chaining for registrations.

Name the parameter of the extension method `services`.

Have the `return services` and `;` at separate lines.

<details>
<summary>Example</summary>

```csharp
// Program.cs
_host.ConfigureServices(services => {
    services
        .AddFooBar()
        .AddBazQux()
})
    
// ServiceCollectionExtensions.cs
public static class ServiceCollectionExtensions
{
    public static IServiceCollection AddFooBar(this IServiceCollection services)
    {
        return services
            .AddSingleton<IFoo, Foo>()
            .AddSingleton<IBar, Bar>();
            ;
    }
    
    public static IServiceCollection AddBazQux(this IServiceCollection services)
    {
        return services
            .AddSingleton<IBaz, Baz>()
            .AddSingleton<IQux, Qux>();
            ;
    }
}
```
</details>

## Git

| Object      | Naming           |
|-------------|------------------|
| Repository  | `SomeRepository` |
| Branch      | `some-branch`    |
| Main branch | `main`           |

## JSON

| Object        | Naming         |
|---------------|----------------|
| Property name | `someProperty` |

Indentation: 4 spaces.

## XML, HTML, XAML

If there is no closing tag for a tag, it should be self-closing.

There should be no leading or trailing whitespaces in tags.

A tag should ether occupy one line with all its attributes, or have each attribute on a separate line.

If each attribute is on a separate line, the first one should be on the same line as the tag, the others should have
their first character at the same column as the first attribute.

When choosing between writing a simple value inside a tag and using an attribute instead, use an attribute.

Indentation: 4 spaces.

<details>
<summary>Example</summary>

```xaml
<StackPanel Orientation="Horizontal">
    <TextBlock Text="Lorem ipsum"
               Background="DodgerBlue"
               Width="150"
               Height="30"/>
</StackPanel>
```
</details>

## Miscellaneous

When applying naming rules, abbreviations should be treated like single words.

<details>
<summary>Example</summary>

Incorrect: `JSONParser`

Correct: `JsonParser`
</details>

In any text files, line width should be no more than 120 characters.