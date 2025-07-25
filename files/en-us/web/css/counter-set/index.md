---
title: counter-set
slug: Web/CSS/counter-set
page-type: css-property
browser-compat: css.properties.counter-set
sidebar: cssref
---

The **`counter-set`** [CSS](/en-US/docs/Web/CSS) property sets [CSS counters](/en-US/docs/Web/CSS/CSS_counter_styles/Using_CSS_counters) on the element to the given values.

If the counters don't exist the `counter-set` property creates a new counter for each named counter in the list of space-separated counter and value pairs. However, to create a new counter it is recommended to use the {{cssxref("counter-reset")}} CSS property.

If a named counter in the list is missing a value, the value of the counter will be set to `0`.

{{InteractiveExample("CSS Demo: counter-set")}}

```css interactive-example-choice
counter-set: none;
```

```css interactive-example-choice
counter-set: chapter-count 0;
```

```css interactive-example-choice
counter-set: chapter-count;
```

```css interactive-example-choice
counter-set: chapter-count 5;
```

```css interactive-example-choice
counter-set: chapter-count -5;
```

```html interactive-example
<section class="default-example" id="default-example">
  <div class="transition-all" id="chapters">
    <h1>Alice's Adventures in Wonderland</h1>
    <h2>Down the Rabbit-Hole</h2>
    <h2 id="example-element">The Pool of Tears</h2>
    <h2>A Caucus-Race and a Long Tale</h2>
    <h2>The Rabbit Sends in a Little Bill</h2>
  </div>
</section>
```

```css interactive-example
#default-example {
  text-align: left;
  counter-set: chapter-count;
}

#example-element {
  background-color: #37077c;
  color: white;
}

h2 {
  counter-increment: chapter-count;
  font-size: 1em;
}

h2::before {
  content: "Chapter " counter(chapter-count) ": ";
}
```

> [!NOTE]
> The counter's value can be incremented or decremented using the {{cssxref("counter-increment")}} CSS property.

## Syntax

```css
/* Set "my-counter" to 0 */
counter-set: my-counter;

/* Set "my-counter" to -1 */
counter-set: my-counter -1;

/* Set "counter1" to 1, and "counter2" to 4 */
counter-set: counter1 1 counter2 4;

/* Cancel any counter that could have been set in less specific rules */
counter-set: none;

/* Global values */
counter-set: inherit;
counter-set: initial;
counter-set: revert;
counter-set: revert-layer;
counter-set: unset;
```

The `counter-set` property is specified as either one of the following:

- A `<custom-ident>` naming the counter, followed optionally by an `<integer>`. You may specify as many counters to reset as you want, with each name or name-number pair separated by a space.
- The keyword value `none`.

### Values

- {{cssxref("custom-ident", "&lt;custom-ident&gt;")}}
  - : The name of the counter to set.
- {{cssxref("&lt;integer&gt;")}}
  - : The value to set the counter to on each occurrence of the element. Defaults to `0` if not specified. If there isn't currently a counter of the given name on the element, the element will create a new counter of the given name with a starting value of `0` (though it may then immediately set or increment that value to something different).
- `none`
  - : No counter set is to be performed. This can be used to override a `counter-set` defined in a less specific rule.

## Formal definition

{{cssinfo}}

## Formal syntax

{{csssyntax}}

## Examples

### Setting named counters

```css
h1 {
  counter-set: chapter section 1 page;
  /* Sets the chapter and page counters to 0,
     and the section counter to 1 */
}
```

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Using CSS Counters](/en-US/docs/Web/CSS/CSS_counter_styles/Using_CSS_counters)
- {{cssxref("counter-increment")}}
- {{cssxref("counter-reset")}}
- {{cssxref("@counter-style")}}
- {{cssxref("counter", "counter()")}} and {{cssxref("counters", "counters()")}} functions
- {{cssxref("content")}} property
- [CSS lists and counters](/en-US/docs/Web/CSS/CSS_lists) module
- [CSS counter styles](/en-US/docs/Web/CSS/CSS_counter_styles) module
