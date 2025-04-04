---
title: "Response: redirected property"
short-title: redirected
slug: Web/API/Response/redirected
page-type: web-api-instance-property
browser-compat: api.Response.redirected
---

{{APIRef("Fetch API")}}{{AvailableInWorkers}}

The **`redirected`** read-only property of the {{domxref("Response")}} interface indicates whether or not the response is the result of a request you made which was redirected.

> [!NOTE]
> Checking `redirected` to prevent redirects is not recommended, because by the time a response is received, the redirect has already happened, and you may have sent the request to an unintended destination, potentially sending sensitive information.
> Instead, you should do the filtering when you call {{domxref("Window/fetch", "fetch()")}}.
> See the example [Disallowing redirects](#disallowing_redirects), which shows this being done.

## Value

A boolean value which is `true` if the response indicates that your request was redirected.

## Examples

### Detecting redirects

Checking to see if the response comes from a redirected request is as simple as checking this flag on the {{domxref("Response")}} object.
In the code below, a textual message is inserted into an element when a redirect occurred during the fetch operation.
Note, however, that this isn't as safe as outright rejecting redirects if they're unexpected, as described under [Disallowing redirects](#disallowing_redirects) below.

The {{domxref("Response.url", "url")}} property returns the final URL after redirects.

```js
fetch("awesome-picture.jpg")
  .then((response) => {
    const elem = document.getElementById("warning-message-box");
    elem.textContent = response.redirected ? "Unexpected redirect" : "";
    // final url obtained after redirects
    console.log(response.url);
    return response.blob();
  })
  .then((imageBlob) => {
    const imgObjectURL = URL.createObjectURL(imageBlob);
    document.getElementById("img-element-id").src = imgObjectURL;
  });
```

### Disallowing redirects

Checking `redirected` is a bad way to prevent redirects, because the redirect has already happened. Instead, you should set the redirect mode to `"error"` in the `options` parameter when calling {{domxref("Window/fetch", "fetch()")}}, like this:

```js
fetch("awesome-picture.jpg", { redirect: "error" })
  .then((response) => response.blob())
  .then((imageBlob) => {
    const imgObjectURL = URL.createObjectURL(imageBlob);
    document.getElementById("img-element-id").src = imgObjectURL;
  });
```

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Fetch API](/en-US/docs/Web/API/Fetch_API)
- [ServiceWorker API](/en-US/docs/Web/API/Service_Worker_API)
- [HTTP access control (CORS)](/en-US/docs/Web/HTTP/Guides/CORS)
- [HTTP](/en-US/docs/Web/HTTP)
