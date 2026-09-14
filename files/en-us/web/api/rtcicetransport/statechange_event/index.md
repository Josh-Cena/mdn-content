---
title: "RTCIceTransport: statechange event"
short-title: statechange
slug: Web/API/RTCIceTransport/statechange_event
page-type: web-api-event
browser-compat: api.RTCIceTransport.statechange_event
---

{{APIRef("WebRTC")}}

A **`statechange`** event occurs when the {{domxref("RTCIceTransport")}} changes state. The {{domxref("RTCIceTransport.state", "state")}} can be used to determine how far through the process of examining, verifying, and selecting a valid candidate pair is prior to successfully connecting the two peers for WebRTC communications.

This event is not cancelable and does not bubble.

## Syntax

Use the event name in methods like {{domxref("EventTarget.addEventListener", "addEventListener()")}}, or set an event handler property.

```js-nolint
addEventListener("statechange", (event) => { })

onstatechange = (event) => { }
```

## Event type

A generic {{domxref("Event")}}.

## Examples

Given an {{domxref("RTCPeerConnection")}}, `pc`, the following code creates an event handler that calls a function named `handleFailure()` if the ICE transport enters a failure state.

```js
let iceTransport = pc.getSenders()[0].transport.iceTransport;

iceTransport.addEventListener("statechange", (ev) => {
  if (iceTransport.state === "failed") {
    handleFailure(pc);
  }
});
```

The same code, using the `onstatechange` event handler property, looks like this:

```js
let iceTransport = pc.getSenders()[0].transport.iceTransport;

iceTransport.onstatechange = (ev) => {
  if (iceTransport.state === "failed") {
    handleFailure(pc);
  }
};
```

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [WebRTC API](/en-US/docs/Web/API/WebRTC_API)
- [WebRTC connectivity](/en-US/docs/Web/API/WebRTC_API/Connectivity)
- The `RTCIceTransport` {{domxref("RTCIceTransport.gatheringstatechange_event", "gatheringstatechange")}} event
- The `RTCIceTransport` {{domxref("RTCIceTransport.selectedcandidatepairchange_event", "selectedcandidatepairchange")}} event
- The `RTCPeerConnection` {{domxref("RTCPeerConnection.negotiationneeded_event", "negotiationneeded")}} event
- The `RTCPeerConnection` {{domxref("RTCPeerConnection.signalingstatechange_event", "signalingstatechange")}} event
- The `RTCPeerConnection` {{domxref("RTCPeerConnection.iceconnectionstatechange_event", "iceconnectionstatechange")}} event
- The `RTCPeerConnection` {{domxref("RTCPeerConnection.icegatheringstatechange_event", "icegatheringstatechange")}} event
- The `RTCPeerConnection` {{domxref("RTCPeerConnection.connectionstatechange_event", "connectionstatechange")}} event
