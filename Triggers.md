# Triggers
## Overview
Triggers are a Downbeat feature where ChronoPaths can be associated with events present in the chart. This is used, for example, to repeat an effect multiple times without having to copy-paste the effect multiple times.

Triggers have a name associated with them and are divided into two parts:
- Trigger events, as in what kind of events to put in the chart.
- Trigger tags, as in how to tag the ChronoPath correctly.

**You cannot use "automatic" as a trigger name**, this is because it's a reserved name used internally in the game. Every other string is valid.

Please refer to [A quick note about parameters](Index.md#a-quick-note-about-parameters) regarding how the syntax is described in this file.

## Trigger events
Trigger events are simple text events with a special syntax that can only be placed on the chart file as a local event:
- `trigger:<name>` : Start event where a trigger is activated.
- `triggerend:<name>` : End event where a trigger gets deactivaed.

Whenever the gameplay time is after a Start event for a trigger, that trigger becomes active.
End events are only required if you need to disable a trigger.
Multiple Start events can be placed without an End event in the middle (it will explained why that is in [how they work](#how-they-work))

## Trigger tags
Trigger tags are just [Path tags](PathFormat.md#tags-syntax) with the prefix `Trigger:` followed by the name.
At the end it looks something like this:
```
(Trigger:foo)
```
*Note that the trigger event has all lowercase "trigger", while the tag starts with a capital letter "Trigger".*

## How they work
When Downbeat wants to calculate a value based on a ChronoPath, it uses a system of overrides in order to get the correct ChronoPath to use for evaluation.

The order of overrides from most important to least important is the following:
- ChronoPaths with an active Trigger
- ChronoPaths with no Trigger associated
- Default ChronoPaths included with the game

If a top layer does not exist Downbeat will use the bottom one.

Normally, for default ChronoPaths and ChronoPaths with no Triggers associated, Downbeat will use the time from the start of the song as the time value used for evaluation.
In the case of a ChronoPath with an active Trigger, this is not necessarily the case: the time value is not from the start of the song, but from the last Start event in the chart that activated this Trigger.

Here is a table to show this behaviour (`automatic` refers to a ChronoPath that has no Trigger associated) :
|Gameplay Time | Path Evaluated | Evaluating time|
|---|---|---|
|0|automatic|0|
|1 --- trigger:foo|foo|**0**|
|2|foo|**1**|
|3|foo|**2**|
|4|foo|**3**|
|5--- trigger:foo|foo|**0**|
|6|foo|**1**|
|7 --- triggerend:foo|automatic|7|
|8|automatic|8|
|9|automatic|9|
|10|automatic|10|

Why is this done? With this system a ChronoPath can be written in such a way that the time value is "relative" and not "absolute".
This also allows resetting the time value multiple times, effectively restarting the effect. For this reason it's possible to have multiple Start events without having an End event.
