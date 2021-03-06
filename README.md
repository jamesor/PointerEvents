# Pointer Events
> A unified event system for the web platform

## Why Pointer Events?

Mouse events and Touch events are fundamentally different beasts in browsers today, and that makes it hard to write cross-platform apps.

For example, a simple finger paint app needs plenty of work to behave correctly with mouse and touch:

Current platforms that implement touch events also provide mouse events for
backward compatibility; however, only a subset of mouse events are fired and the
semantics are changed.

- Mouse events are only fired after the touch sequence ends.
- Mouse events are not fired on elements without a click event handler. One must
  be attached by default, or directly on the element with “onclick”.
- Click events are not fired if the content of the page changes in a mousemove
  or mouseover event.
- Click events are fired 300ms after the touch sequence ends.
- More information: [Apple Developer Documentation](http://developer.apple.com/library/safari/#documentation/appleapplications/reference/safariwebcontent/HandlingEvents/HandlingEvents.htmls).

Additionally, Touch events are sent only to the element that received the
touchstart. This is fundamentally different than mouse events, which fire on the
element that is under the mouse. To make them behave similarly, touch events
need to be retargeted with `document.elementFromPoint`.

These incompatibilities lead to applications having to listen to 2 sets of events, mouse on
desktop and touch for mobile.

**This forked interaction experience is cumbersome and hard to maintain.**

Instead, there should exist a set of events that are normalized such that they
behave exactly the same, no matter the source: touch, mouse, stylus, skull
implant, etc. To do this right, this normalized event system needs to be
available for all the web platform to use.

*Thus, PointerEvents!*

## Why not other event systems?

There are existing implementations of event unification, but each have caveats
that we think make them not suitable:
- [pointer.js](https://github.com/borismus/pointer.js) nicely abstracts mouse and touch events, but does not solve the touch event targeting issue, and wraps addEventListener
- [Hammer.js](http://eightmedia.github.com/hammer.js/) requires wrapping all elements in a handler and does not dispatch DOM events
- [Sencha’s solution](http://www.sencha.com/products/touch) requires the whole Sencha Touch framework
- [Microsoft MSPointer Events](http://msdn.microsoft.com/en-us/library/ie/hh673557.aspx) solve the problem the best, but aren't standardized.

Therefore, we felt the need to create PointerEvents.

## Where can I use PointerEvents?

PointerEvents should work on all "Evergreen" (self-updating) browsers.

So far, it has been tested on Chrome.

## How does PointerEvents work?

The PointerEvents library listens to native platform events on the document
level and dispatches new DOM events to the original target. This means that any
addEventListener calls made before PointerEvents initializes will still work,
and we can utilize the native event bubbling. In addition, our architecture is
very modular, so any pieces a developer doesn't can be excluded. This keeps the
code base small and nimble. The events generated by PointerEvents include all
the information that can be expected from MouseEvents, and other modules can add
even more information.

## What events are there?

- Basic screen movement
  - `pointermove`: a pointer moves, similar to touchmove or mousemove.
  - `pointerdown`: a pointer is activated, or a device button held.
  - `pointerup`: a pointer is deactivated, or a device button released.
  - `pointerenter`: a pointer crosses into an element’s bounding box.
  - `pointerleave`: a pointer leaves an element’s bounding box.
  - `pointertap`: an up/down pair is seen in rapid succession.
- Hold Events **not yet implemented**
  - `pointerhold`: a pointer is held down for 200ms.
  - `pointerholdpulse`: a pointer is *still* held down, pulses every 200ms.
  - `pointerrelease`: a pointer is released after holding.
- Drag Events **not yet implemented**
  - `pointerdragstart`: a pointer starts dragging across the screen.
  - `pointerdrag`: a pointer is being dragged across the screen, updates with
    position, fired on the target of the dragstart.
  - `pointerdragend`: a pointer stops dragging, fired on the target of the
    dragstart.
  - `pointerdrop`: a pointer stops dragging, fired on the last element the
    pointer entered.
  - `pointerdragenter`: a pointer enters an element's bounding box while
    dragging.
  - `pointerdragleave`: a pointer leaves an element’s bounding box while
    dragging.
  - `pointerflick`: a pointer moves rapidly across the screen, provides inertial
    information.
- Gesture Events **not yet implemented**
  - `pointerscale`: two or more pointers are spreading apart
  - `pointerrotate`: two or more pointers are rotating around a center point
