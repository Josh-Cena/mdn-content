---
title: "TypeError: invalid 'instanceof' operand 'x'"
slug: Web/JavaScript/Reference/Errors/invalid_right_hand_side_instanceof_operand
page-type: javascript-error
sidebar: jssidebar
---

The JavaScript exception "invalid 'instanceof' operand" occurs when the right-hand side
operands of the [`instanceof` operator](/en-US/docs/Web/JavaScript/Reference/Operators/instanceof)
isn't used with a constructor object, i.e., an object which has a `prototype` property and is callable.

## Message

```plain
TypeError: Right-hand side of 'instanceof' is not an object (V8-based)
TypeError: invalid 'instanceof' operand "x" (Firefox)
TypeError: Right hand side of instanceof is not an object (Safari)

TypeError: Right-hand side of 'instanceof' is not callable (V8-based)
TypeError: x is not a function (Firefox)
TypeError: x is not a function. (evaluating 'x instanceof y') (Safari)

TypeError: Function has non-object prototype 'undefined' in instanceof check (V8-based)
TypeError: 'prototype' property of x is not an object (Firefox)
TypeError: instanceof called on an object with an invalid prototype property. (Safari)
```

## Error type

{{jsxref("TypeError")}}

## What went wrong?

The [`instanceof` operator](/en-US/docs/Web/JavaScript/Reference/Operators/instanceof) expects
the right-hand-side operands to be a constructor object,
i.e., an object which has a `prototype` property and is callable. It can also be an object with a [`Symbol.hasInstance`](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/hasInstance) method. This error can occur if:

- The right-hand side operand is not an object.
- The right-hand side operand is not a callable and it has no `Symbol.hasInstance` method.
- The right-hand side operand is a callable, but its [`prototype`](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/prototype) property is not an object. (For example, [arrow functions](/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) do not have a `prototype` property.)

## Examples

### instanceof vs. typeof

```js example-bad
"test" instanceof ""; // TypeError: invalid 'instanceof' operand ""
42 instanceof 0; // TypeError: invalid 'instanceof' operand 0

function Foo() {}
const f = Foo(); // Foo() is called and returns undefined
const x = new Foo();

x instanceof f; // TypeError: invalid 'instanceof' operand f
x instanceof x; // TypeError: x is not a function
```

To fix these errors, you will either need to replace
the [`instanceof` operator](/en-US/docs/Web/JavaScript/Reference/Operators/instanceof)
with the [`typeof` operator](/en-US/docs/Web/JavaScript/Reference/Operators/typeof),
or to make sure you use the function name, instead of the result of its evaluation.

```js example-good
typeof "test" === "string"; // true
typeof 42 === "number"; // true

function Foo() {}
const f = Foo; // Do not call Foo.
const x = new Foo();

x instanceof f; // true
x instanceof Foo; // true
```

## See also

- [`instanceof`](/en-US/docs/Web/JavaScript/Reference/Operators/instanceof)
- [`typeof`](/en-US/docs/Web/JavaScript/Reference/Operators/typeof)
