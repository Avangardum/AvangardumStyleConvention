# Avangardum's style convention

This is the convention for my personal projects, covering various aspects of development.

## Style convention versioning

Current version: 4.0

Each project using this convention should have a file named StyleConvention.md in its root directory, containing the
name, a used version and a link to a corresponding version branch of this repository, like this:

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
| Synchronous function                       | `SomeFunction`      |
| Asynchronous function                      | `SomeFunctionAsync` |
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

All enum constants should have an integer value explicitly specified.

The first enum constant should have the 0 value.

<details>
<summary>Example</summary>

```csharp
// incorrect
public enum Season { None, Winter, Spring, Summer, Autumn }

// correct
public enum Season 
{ 
    Winter = 0, 
    Spring = 1, 
    Summer = 2, 
    Autumn = 3 
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

    public event EventHandler<DiedEventArgs> Died;

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

### Methods, properties, indexers

Do not use expression body declaration if it is more than one line long. Having a signature and an expression body on
different lines is still acceptable.

<details>
<summary>Example</summary>

```csharp
// incorrect
private List<User> GetTargetUsers() => users
    .Where(u => MinTargetAge <= u.Age && u.Age <= MaxTargetAge)
    .Where(u => u.IsVerified)
    .Where(u => u.IsOnlnie)
    .Where(u => !_previouslyTargetedUsers.Countains(u))
    .ToList();


// correct
private List<User> GetTargetUsers()
{
    return users
        .Where(u => MinTargetAge <= u.Age && u.Age <= MaxTargetAge)
        .Where(u => u.IsVerified)
        .Where(u => u.IsOnlnie)
        .Where(u => !_previouslyTargetedUsers.Countains(u))
        .ToList();
}
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


### Formatting

These rules are the recommended formatting. But if in any particular case other formatting is more readable, it can also
be used.

Even if the following rules say that certain members should not have a newline between them, it is still acceptable to
divide them into groups with a single newline between groups.

There should be no whitespace between a method name and its parameters.

There should be a single whitespace between a loop or branching operator and its condition.

There should be no newline between fields and or between auto-properties with no initializer or with a same line
initializer.

There should be a single newline between fields or between auto-properties with a separate line(s) initializer.

There should be no newline between properties with a same line expression body getter or abstract properties.

There should be a single newline between properties with a block or a separate line expression body getter
(and between them and same line expression body or auto-properties).

There should be no newline between functions with a same line expression body or a same line empty body or 
abstract methods.

There should be a single newline between functions with a block or a separate line expression body
(and between them and same line expression body functions).

There should be a single newline between different kinds of members (fields, properties, methods, etc.).

If there is a block of code in parentheses, square brackets or curly braces, spanning multiple lines,
the opening and closing symbols should be on their own lines.

Indentation: 4 spaces.

<details>
<summary>Example</summary>

```csharp
// incorrect
private int _a;

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

When using a switch statement or expression, there should always be a default case. If default case is invalid, 
it should throw an ArgumentOutOfRangeException with no parameters.

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

## Miscellaneous

When applying naming rules, abbreviations should be treated like single words.

<details>
<summary>Example</summary>

Incorrect: `JSONParser`

Correct: `JsonParser`
</details>

In any text files, line width should be no more than 120 characters.