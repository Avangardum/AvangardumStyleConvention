# Avangardum's style convention

## Style convention versioning

Current version: 13.0

Each project using this convention should have a file named StyleConvention.md in its root directory, containing the
name, a used version, a link to a corresponding version branch of this repository and the text of this convention, 
omitting irrelevant sections while leaving a notice that they are omitted, like this:

```markdown
This project uses Avangardum's style convention v1.0

https://github.com/Avangardum/AvangardumStyleConvention/tree/version/1.0

# Local rules (take precedence over the base convention)

local rules

# Avangardum's style convention v1.0 (irrelevant sections omitted)

this convention
```

Any project specific rules should be written in this file.

Version has the following format: major.minor[.patch]

Major version is incremented when there are breaking changes (upgrading to a new version may require existing code
changes).

Minor version is incremented when there are non-breaking changes.

Patch version is incremented when there are fixes that don't change the essence of the convention.

## C#

### Naming

| Object                                     | Naming               |
|--------------------------------------------|----------------------|
| Namespace                                  | `SomeNamespace`      |
| Non-interface type                         | `SomeType`           |
| Interface                                  | `ISomeInterface`     |
| Non-local constant / static readonly field | `SomeConstant`       |
| Private field                              | `_someField`         |
| Non-private field                          | `SomeField`          |
| Event                                      | `SomeEvent`          |
| Property                                   | `SomeProperty`       |
| Function that doesn't return Task          | `SomeFunction`       |
| Function that returns Task                 | `SomeFunctionAsync`  |
| Record primary constructor parameter       | `SomeParameter`      |
| Other parameter                            | `someParameter`      |
| Local variable                             | `someVariable`       |
| Local constant                             | `someConstant`       |
| Region                                     | `SomeRegion`         |
| Goto label                                 | `SomeGotoLabel`      |
| Tuple / anonymous type member              | `SomeMember`         |
| Generic type parameter                     | `TSomeTypeParameter` |

### Comments

Comments should be written in English.

Comments should be written using `//`, not `/* */`.

`//` should always be followed by a whitespace.

If `//` is not in the beginning of a line, it should also be preceded by a whitespace.

Use XML comments to describe the public interface of a type.

Use `<inheritdoc />` wherever possible to make it obvious that there is documentation available.

### Namespaces

Namespaces should start with `DeveloperName.RepositoryName`.

Use file-scoped namespaces.

Namespaces should correspond to the folder structure (use automatic namespace adjustment in your IDE).

### Types

Each source code file should contain only one non-nested type.

Classes that are not meant to be inherited from, but are meant to be instantiated, should be sealed.

Classes that are not meant to be instantiated, but are meant to be inherited from, should be abstract.

Classes that are meant to be neither instantiated, nor inherited from, should be static.

Inside a constructor, when choosing between reading a parameter and reading a field, prefer reading a parameter.

When a type is instantiated using a parameterless constructor with property initializers, the parentheses
should be omitted.

If a nested type name conflicts with a property / field name, the nested type should be prefixed with `T`.

### Enums

Non-flag enums shouldn't have a `None` value. For the purpose of representing nothing use `null`.

Non-flag enum constants shouldn't have an integer value explicitly specified.

### Delegates, events

Prefer using `Action` or `Func` delegate types, avoid `Predicate`, `EventArgs`.

Event argument types should follow one of the following patterns: *event* + Args (`UpdatingArgs`),
*sender* + *event* + Args (`DataSourceUpdatingArgs`).

Event handler method names should follow one of the following patterns: On + *event* (`OnUpdating`), 
On + *sender* + *event* (`OnDataSourceUpdating`).

Events should not be nullable, they should be initialized with an empty lambda.

Events that are invoked before something happens should be named as verbs in present continuous 
(`DataSourceUpdating`).

Events that are invoked after something happens should be named as verbs in past simple. (`DataSourceUpdated`).

### Functions, properties, indexers

Do not use expression body declaration if it is more than one line long. Having a signature and an expression body on
different lines is still acceptable.

Do not mutate primary constructor arguments.

Do not use the `delegate` keyword for declaring anonymous functions. Use lambdas instead.

Abstract methods' optional parameters should have their default value set to null. What value is actually used, if it
is not specified, is an implementation detail and should be specified in an implementation.

In `Task` returning functions, don't directly return a `Task` returned by another function. Instead, await it and return
its result.

### Collections

Use collection expressions where possible (`ImmutableList<int> a = [0, 1, 2];`).

Use immutable collections (`ImmutableList`, `ImmutableHashSet`, etc.) where possible.

Functions should both accept and return interfaces of a specific collection type (`IReadOnlyList`, `IDictionary`),
preferentially read only versions.

### Formatting

There should be no whitespace between a method name and its parameters.

In a `new` expression there should be no whitespace between an object type and its constructor parameters.
For target-typed `new`, a single whitespace should exist between `new` and parentheses (`new Foo()`).

In a `new` expression there should be a single whitespace between constructor parameters and field initializers, or, in
case if there are no constructor parameters, between an object type and field initializers. (`new Foo { Value = 1 }`)

There should be a single whitespace between `do`, `while`, `if`, `using`, `fixed` and following parentheses.

Using `if` or loops without curly braces is only allowed if the body is on the same line as `if` or loop operator
(`if (value == null) return;`).

If there is a block of code in parentheses, square brackets, angle brackets or curly braces, spanning multiple lines,
each element and each opening / closing symbol should be on its own line.

Indentation: 4 spaces.

Having a single newline between members of a type is always acceptable. However, it is possible to omit it if
both members occupy only 1 line each (including comments, attributes, initializers).

Don't use trailing commas.

When having a comma-separated or semicolon-separated list of elements, each comma should be followed by a whitespace
or a newline.

When doing a line break on an operator, the operator should be at the end of a line, not at the beginning.
The exceptions are the `.`, `?.`, `!.` operators, which should be at the beginning of a line.

When having an inline block of parentheses, square brackets or angle brackets, there should be no whitespace between
the opening and closing symbols and the content.

When having an inline block of curly braces, there should be a whitespace between the opening and closing braces and
the content.

There should be a whitespace or a newline between a binary/ternary operator and its operands (except `.`, `?.`, `!.`).

There should be no whitespace between a unary operator and its operand.

All cases of a switch statement should be wrapped in curly braces.

### Miscellaneous

When comparing a value with boundary values, all the values should be sorted in ascending order from left to right
(`min <= value && value <= max`).

Use `var` only when the type is explicitly named on the right-hand side.

Use target-typed `new` only when the type is explicitly named on the left-hand side.

When choosing between `var` and target-typed `new`, prefer target-typed `new`.

Use explicit privacy modifiers where possible. The exception are interface members, they should be implicitly public.

When calling `ToString` (whether explicitly or implicitly, using string interpolation or concatenation), which can
accept a format string or culture info, such data should be passed explicitly.

Do not use the `file` access modifier.

In internal types, do not use the `internal` access modifier on their members, instead use `public`.

Use the most restrictive access modifier possible
(`private` > `private protected` > `protected` / `internal` > `protected internal` > `public`).

Use modifiers in the following order (modifiers on the same line are mutually exclusive):

- `private`, `private protected`, `protected`, `internal`, `protected internal`, `public`
- `new`
- `static`, `sealed`, `abstract`, `virtual`, `override`
- `extern`, `unsafe`, `async`, `const`, `readonly`, `volatile`
- `partial`

Do not sort type members by any formal properties (accessibility, type(property, method...)), instead prefer locality,
placing members that use each other nearby, such as placing a method declaration after a method that calls it.

## Files

| Object | Naming     |
|--------|------------|
| File   | `SomeFile` |

## Unity

| Object                                                  | Naming       |
|---------------------------------------------------------|--------------|
| All Unity objects (Game objects, Animator states, etc.) | `SomeObject` |

If you need to have serializable enums that show in the inspector, do not use your business logic enums. Instead, create
special serializable enums with the `Serializable` prefix, use them anywhere where you need serialization, and use the 
business logic enums in your business logic, mapping them on the boundary. Unlike the business logic enums, the 
serializable enums can have a `None` value, and their constants should have integer values explicitly specified, 
starting from 1000 with the default step of 1000 to allow inserting new values in between in the future.

## NUnit

| Object     | Naming                                                       |
|------------|--------------------------------------------------------------|
| Test class | `SomeClassTests`                                             |
| Test       | `SomeMethod_SomeStateAndArgs_SomeResult` (no `Async` suffix) |

Each test class should be either abstract or sealed.

In sealed test classes, setup and teardown methods should be named `SetUp` and `TearDown`. In abstract test classes, 
their names should be prefixed with the class name, like `TestsBaseSetUp`.

## XUnit

| Object     | Naming                                                       |
|------------|--------------------------------------------------------------|
| Test class | `SomeClassTests`                                             |
| Test       | `SomeMethod_SomeStateAndArgs_SomeResult` (no `Async` suffix) |

Each test class should be either abstract or sealed.

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

Inline self-closing tag should have a space before `/>`. 

A tag should either occupy one line with all its attributes, or have each attribute on a separate line, with tag name
and closing angle bracket on its own line.

Indentation: 4 spaces.

## Miscellaneous

Abbreviations with 2 characters should be fully capitalized (`IOStream`), longer abbreviations follow same naming rules
as if they were regular words (`JsonParser`).

Whenever possible, format code to avoid line width exceeding 120 symbols.

In any text files, there should be no trailing whitespaces.

All files should end with an empty line.
