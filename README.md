# Avangardum's Convention
This is the convention for my personal projects, covering various aspects of development.

## C#

### Naming
| Object                                     | Naming           |
|--------------------------------------------|------------------|
| Namespace                                  | `SomeNamespace ` |
| Class / struct / record / enum / delegate  | `SomeType`       |
| Interface                                  | `ISomeInterface` |
| Public field                               | `SomeField `     |
| Non-public field                           | `_someField `    |
| Non-local Constant / static readonly field | `SomeConstant `  |
| Property                                   | `SomeProperty `  |
| Event                                      | `SomeEvent `     |
| Method                                     | `SomeMethod  `   |
| Method parameter                           | `someParameter ` |
| Local variable                             | `someVariable `  |
| Local constant                             | `someConstant`   |
| Region                                     | `SomeRegion`     |
| Goto label                                 | `SomeGotoLabel`  |

### Comments

Comments should be written in English.

Comments should be written using `//`, not `/* */`.

`//` should always be followed by a whitespace.

If `//` is not in the beginning of a line, it should also be preceded by a whitespace.

### Namespaces

Root namespace for the project should follow the following pattern: *Developer/Company*.*Project* 
(examples: `Avangardum.TwilightRun`, `Hyperbox.DroneTanksArena`).

### Types

Each source code file should contain only one non-nested type.

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
- internal
- protected internal
- protected
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

Event argument types should have the `EventArgs` suffix.

Event handler method name should follow one of the following patterns: On + *event* (example: `OnDying`), On + *sender* + *event*
(example: `OnCharacterDied`).

Event handler methods should have 2 parameters: `sender` and `e`.

Events should not be initialized with an empty delegate.

Events should be invoked using the `Invoke` method.

Events should be invoked using the `?.` operator.

Events that are invoked before something happens should be named as verbs in present continuous.

Events that are invoked after something happens should be named as verbs in past simple.

<details>
<summary>Example</summary>

```csharp
// incorrect
public class Character
{
    public string Name { get; set; }

    public event Action<Character, Character> Death = delegate {};
    
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

    public string Name { get; set; }

    public event EventHandler<DiedEventArgs> Died;
    
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

### Miscellaneous

When comparing a value to with boundary values, all of the values should be sorted in ascending order from left to right.

<details>
<summary>Example</summary>

```csharp
// incorrect
var isValid = value >= min && value <= max;

// correct
var isValid = min <= value && value <= max;
```
</details>

## Files

File naming: `Some File`. The only exception are source code files, they should be named same as a type / module contained in them.

## Unity

All Unity objects (Game objects, Animator states, etc.) naming: `Some Object`

## Zenject

Binding ID naming: `SomeBinding`

Injection methods priority (from most recommended to least recommended):
- Injection using constructor
- Injection using an injection method
- Injection using injectable fields/properties

A class should have no more than one injection method.

An injection method should be named `Inject`. If a class has a base class that has an injection method defined, 
its own injection method should have the `C` prefix (`CInject`). Overriding injection methods or hiding them
with the `new` keyword is not allowed.

## NUnit

Use the constraint model for assertions where possible.

When asserting that an object, inheriting from UnityEngine.Object, is null or not null, use `UnityEngine.Assertions.Assert`
instead of `NUnit.Framework.Assert`. For this purpose use the following type alias: `using UAssert = UnityEngine.Assertions.Assert;`

A test suite should have no more than one setup and teardown methods.

Setup and teardown methods should be named `SetUp` and `TearDown`. If a fixture has a base class that has setup/teardown
methods defined, its own setup/teardown methods should have the `C` prefix (`CSetUp`, `CTearDown`). 
Overriding these methods or hiding them with the `new` keyword is not allowed.

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

Branch naming: `some-branch`

Main branch name: `main`

## Miscellaneous

When applying naming rules, abbreviations should be treated like single words.

<details>
<summary>Example</summary>

Incorrect: `JSONParser`

Correct: `JsonParser`
</details>