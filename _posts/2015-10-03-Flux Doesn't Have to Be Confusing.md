---
layout: post
title: Flux Doesn't Have to Be Confusing
published: true
---

[Facebook Flux](https://facebook.github.io/flux/docs/overview.html) is application architecture developed by Facebook. I found it quite similar to command-query-segregation pattern we implemented at work [bankerstoolbox.com](http://www.bankerstoolbox.com), except somehow it was quite confusing.

The diagram looks simple.

![Excerpt from https://facebook.github.io/flux/docs/overview.html]({{ BASE_PATH }}/img/2015-10-03-flux-simple-f8-diagram-explained-1300w.png)

But when you try to understand sample code, this diagram has couple of issues.

1. Is Action a function that makes change, or data that represents what has been changed?
2. Is Action called by a component the same as action passed to store?
3. Where is action creator in my code?
    * If it is important to note in the diagram, it should be in the code structure.

When you don't recognize these contextual differences, your code can be very confusing to read through.
The action called by a view will always be a command (i.e. loadData(), submitReservation()). Once action does the job, the resulting message to the store can be as-is for commands like loadData (Action is sending Store data so that Store will load data), or an event (Action submitted a reservation and letting Store know about the result).

Little tweak with the naming makes things clearer.

1. Action is a function that makes change. In the diagram, Action Creators are now Actions.
2. ActionMessage is initiated by Action and received by Store.
3. ActionMessageType is what used to be ActionType. ActionMessageType can be simple present or past tense.

New diagram looks like this:

![New Flux diagram with ActionResult]({{ BASE_PATH }}/img/2015-10-03-flux%20with%20ActionResult.png)

Apply to your code, and see if it makes clearer!