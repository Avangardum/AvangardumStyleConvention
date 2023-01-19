# Avangardum Convention
This is the convention for my personal projects, covering various aspects of development

## C#

### Naming
| Object                                       | Naming           |
|----------------------------------------------|------------------|
| Type (class, struct, record, enum, delegate) | `SomeClass `     |
| Interface                                    | `ISomeInterface` |
| Public field                                 | `SomeField `     |
| Non-public field                             | `_someField `    |
| Property                                     | `SomeProperty `  |
| Event                                        | `SomeEvent `     |
| Method                                       | `SomeMethod  `   |
| Method parameter                             | `someParameter ` |
| Local variable                               | `someVariable `  |
| Namespace                                    | `SomeNamespace ` |
| Non-local Constant / static readonly field   | `SomeConstant `  |
| Local constant                               | `someConstant`   |
| Region                                       | `SomeRegion`     |
| Goto label                                   | `SomeGotoLabel`  |

### Comments

Comments should be written in English.

Comments should be written using `//`, not `/* */`.

`//` should always be followed by a whitespace.

If `//` is not in the beginning of a line, it should also be preceded by a whitespace.

### Namespaces

Root namespace for the project should follow the following pattern: *Developer*.*Project* (example: `Avangardum.TwilightRun`).

### Types

Each source file should contain only one non-nested type.

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



### Events

Events should use the `EventHandler` delegate. If there are no arguments to pass, `EventArgs.Empty` should be passed. 
If there are arguments to pass, the generic version of EventHandler should be used and arguments should be passed 
in an event arguments object.

Event argument types should have the `EventArgs` suffix.

Event handler method name should follow one of the following patterns: On + *event* (example: `OnDying`), On + *sender* + *event*
(example: `OnCharacterDied`).

Event handler methods should have 2 parameters: `sender` and `e`.

Events should not be initialized with an empty delegate.

Events should be invoked using the `Invoke` method.

Events should be invoked using the `.?` operator.

Events that are invoked before something happens should be verbs in present continuous

Events that are invoked after something happens should be verbs in past simple.

<details>
<summary>Example</summary>

```csharp
// incorrect
public class Character
{
    public string Name { get; set; }

    public event Action<Character> Death = delegate {};
    
    private void Die(Charater killer)
    {
        Death(killer);
    }
}

public class KillReporter()
{
    private void OnKill(object objVictim, Character killer)
    {
        var victim = (Character)objVictim;
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
        Died?.Invoke(new DiedEventArgs(killer));
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

## NUnit



## Git



## Miscellaneous

When applying naming rules, abbreviations should be treated like single words.

<details>
<summary>Example</summary>

Incorrect: `JSONParser`

Correct: `JsonParser`
</details>