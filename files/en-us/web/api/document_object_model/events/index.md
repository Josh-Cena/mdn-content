---
title: DOM events
short-title: Working with events
slug: Web/API/Document_Object_Model/Events
page-type: guide
spec-urls:
  - https://dom.spec.whatwg.org/#events
  - https://html.spec.whatwg.org/multipage/indices.html#events-2
---

{{DefaultAPISidebar("DOM")}}

[Events](/en-US/docs/Learn_web_development/Core/Scripting/Events) are fired to notify code of "interesting changes" that may affect code execution. These can arise from user interactions such as using a mouse or resizing a window, changes in the state of the underlying environment (e.g., low battery or media events from the operating system), and other causes.

Each event is represented by an object that is based on the {{domxref("Event")}} interface, and may have additional custom fields and/or functions to provide information about what happened. The documentation for every event has a table (near the top) that includes a link to the associated event interface, and other relevant information. A full list of the different event types is given in [Event > Interfaces based on Event](/en-US/docs/Web/API/Event#interfaces_based_on_event).

This topic provides an index to the main _sorts_ of events you might be interested in (animation, clipboard, workers etc.) along with the main classes that implement those sorts of events.

## Creating and dispatching events

In addition to the events fired by built-in interfaces, you can create and dispatch DOM events yourself. Such events are commonly called _synthetic events_, as opposed to the events fired by the browser.

### Creating custom events

Events can be created with the [`Event`](/en-US/docs/Web/API/Event) constructor as follows:

```js
const event = new Event("build");

// Listen for the event.
elem.addEventListener("build", (e) => {
  /* … */
});

// Dispatch the event.
elem.dispatchEvent(event);
```

This code example uses the [EventTarget.dispatchEvent()](/en-US/docs/Web/API/EventTarget/dispatchEvent) method.

### Adding custom data – CustomEvent()

To add more data to the event object, the [CustomEvent](/en-US/docs/Web/API/CustomEvent) interface exists and the **detail** property can be used to pass custom data.
For example, the event could be created as follows:

```js
const event = new CustomEvent("build", { detail: elem.dataset.time });
```

This will then allow you to access the additional data in the event listener:

```js
function eventHandler(e) {
  console.log(`The time is: ${e.detail}`);
}
```

### Adding custom data – subclassing Event

The [`Event`](/en-US/docs/Web/API/Event) interface can also be subclassed. This is particularly useful for reuse, or for more complex custom data, or even adding methods to the event.

```js
class BuildEvent extends Event {
  #buildTime;

  constructor(buildTime) {
    super("build");
    this.#buildTime = buildTime;
  }

  get buildTime() {
    return this.#buildTime;
  }
}
```

This code example defines a `BuildEvent` class with a read-only property and a fixed event type.

The event could then be created as follows:

```js
const event = new BuildEvent(elem.dataset.time);
```

The additional data can then be accessed in the event listeners using the custom properties:

```js
function eventHandler(e) {
  console.log(`The time is: ${e.buildTime}`);
}
```

### Event bubbling

It is often desirable to trigger an event from a child element and have an ancestor catch it; optionally, you can include data with the event:

```html
<form>
  <textarea></textarea>
</form>
```

```js
const form = document.querySelector("form");
const textarea = document.querySelector("textarea");

// Create a new event, allow bubbling, and provide any data you want to pass to the "detail" property
const eventAwesome = new CustomEvent("awesome", {
  bubbles: true,
  detail: { text: () => textarea.value },
});

// The form element listens for the custom "awesome" event and then consoles the output of the passed text() method
form.addEventListener("awesome", (e) => console.log(e.detail.text()));

// As the user types, the textarea inside the form dispatches/triggers the event to fire, using itself as the starting point
textarea.addEventListener("input", (e) => e.target.dispatchEvent(eventAwesome));
```

### Creating and dispatching events dynamically

Elements can listen for events that haven't been created yet:

```html
<form>
  <textarea></textarea>
</form>
```

```js
const form = document.querySelector("form");
const textarea = document.querySelector("textarea");

form.addEventListener("awesome", (e) => console.log(e.detail.text()));

textarea.addEventListener("input", function () {
  // Create and dispatch/trigger an event on the fly
  // Note: Optionally, we've also leveraged the "function expression" (instead of the "arrow function expression") so "this" will represent the element
  this.dispatchEvent(
    new CustomEvent("awesome", {
      bubbles: true,
      detail: { text: () => textarea.value },
    }),
  );
});
```

## Triggering built-in events

This example demonstrates simulating a click (that is programmatically generating a click event) on a checkbox using DOM methods. [View the example in action.](https://mdn.dev/archives/media/samples/domref/dispatchEvent.html)

<!-- cSpell:ignore cancelled -->

```js
function simulateClick() {
  const event = new MouseEvent("click", {
    view: window,
    bubbles: true,
    cancelable: true,
  });
  const cb = document.getElementById("checkbox");
  const cancelled = !cb.dispatchEvent(event);

  if (cancelled) {
    // A handler called preventDefault.
    alert("cancelled");
  } else {
    // None of the handlers called preventDefault.
    alert("not cancelled");
  }
}
```

## Registering event handlers

There are two recommended approaches for registering handlers. Event handler code can be made to run when an event is triggered either by assigning it to the target element's corresponding _onevent_ property or by registering the handler as a listener for the element using the {{domxref("EventTarget.addEventListener", "addEventListener()")}} method. In either case, the handler will receive an object that conforms to the [`Event` interface](/en-US/docs/Web/API/Event) (or a [derived interface](/en-US/docs/Web/API/Event#interfaces_based_on_event)). The main difference is that multiple event handlers can be added (or removed) using the event listener methods.

> [!WARNING]
> A third approach for setting event handlers using HTML onevent attributes is not recommended! They inflate the markup and make it less readable and harder to debug. For more information, see [Inline event handlers](/en-US/docs/Learn_web_development/Core/Scripting/Events#inline_event_handlers_—_dont_use_these).

### Using onevent properties

By convention, JavaScript objects that fire events have corresponding "onevent" properties (named by prefixing "on" to the name of the event). These properties are called to run associated handler code when the event is fired, and may also be called directly by your own code.

To set event handler code, you can just assign it to the appropriate onevent property. Only one event handler can be assigned for every event in an element. If needed, the handler can be replaced by assigning another function to the same property.

The following example shows how to set a `greet()` function for the `click` event using the `onclick` property.

```js
const btn = document.querySelector("button");

function greet(event) {
  console.log("greet:", event);
}

btn.onclick = greet;
```

Note that an object representing the event is passed as the first argument to the event handler. This event object either implements or is derived from the {{domxref("Event")}} interface.

### EventTarget.addEventListener

The most flexible way to set an event handler on an element is to use the {{domxref("EventTarget.addEventListener")}} method. This approach allows multiple listeners to be assigned to an element and enables listeners to be _removed_, if needed, using {{domxref("EventTarget.removeEventListener")}}.

> [!NOTE]
> The ability to add and remove event handlers allows you to, for example, have the same button performing different actions in different circumstances. In addition, in more complex programs, cleaning up old/unused event handlers can improve efficiency.

The following example shows how a `greet()` function can be set as a listener/event handler for the `click` event (you could use an anonymous function expression instead of a named function if desired). Note again that the event is passed as the first argument to the event handler.

```js
const btn = document.querySelector("button");

function greet(event) {
  console.log("greet:", event);
}

btn.addEventListener("click", greet);
```

The method can also take additional arguments/options to control aspects of how the events are captured and removed. More information can be found on the {{domxref("EventTarget.addEventListener")}} reference page.

#### Using AbortSignal

A notable event listener feature is the ability to use an abort signal to clean up multiple event handlers at the same time.

This is done by passing the same {{domxref("AbortSignal")}} to the {{domxref("EventTarget/addEventListener()", "addEventListener()")}} call for all the event handlers that you want to be able to remove together. You can then call {{domxref("AbortController/abort()", "abort()")}} on the controller owning the `AbortSignal`, and it will remove all event handlers that were added with that signal. For example, to add an event handler that we can remove with an `AbortSignal`:

```js
const controller = new AbortController();

btn.addEventListener(
  "click",
  (event) => {
    console.log("greet:", event);
  },
  { signal: controller.signal },
); // pass an AbortSignal to this handler
```

This event handler can then be removed like this:

```js
controller.abort(); // removes any/all event handlers associated with this controller
```

### Interaction of multiple event handlers

The `onevent` IDL property (for example, `element.onclick = ...`) and the HTML `onevent` content attribute (for example, `<button onclick="...">`) both target the same single handler slot. HTML loads before JavaScript could access the same element, so usually JavaScript replaces what's specified in HTML. Handlers added with {{domxref("EventTarget.addEventListener", "addEventListener()")}} are independent. Using `onevent` does not remove or replace listeners added with `addEventListener()`, and vice versa.

When an event is dispatched, listeners are called in phases. There are two phases: _capture_ and _bubble_. In the capture phase, the event starts from the highest ancestor element and moves down the DOM tree until it reaches the target. In the bubble phase, the event moves in the opposite direction. Event listeners by default listen in the bubble phase, and they can listen in the capturing phase by specifying `capture: true` with `addEventListener()`. Within a phase, listeners run in the order they were registered. The `onevent` handler is registered the first time it becomes non-null; later reassignments change only its callback, not its position in the order.

Calling {{domxref("Event.stopPropagation()")}} prevents calling listeners on other elements later in the propagation chain. {{domxref("Event.stopImmediatePropagation()")}} also prevents calling remaining listeners on the same element.

## Specifications

{{Specifications}}

## See also

- [Introduction to events](/en-US/docs/Learn_web_development/Core/Scripting/Events)
- [Event bubbling](/en-US/docs/Learn_web_development/Core/Scripting/Event_bubbling)
