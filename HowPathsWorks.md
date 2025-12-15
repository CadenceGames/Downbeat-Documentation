# Understanding how paths work in Downbeat
## Overview
The goal of this file is to explain the concept of paths and how they are used by Downbeat for various parts of gameplay.
These paths are written in a file that follows the syntax described in [PathFormat.md](PathFormat.md).
It is *not* required to know the syntax in advance in order to follow this guide, but it is recommended to read [A quick note about parameters ](README.md#a-quick-note-about-parameters) before continuing on with this file.

## Introduction and Foundation concepts
### Turtle Graphics

Imagine that you have a pen and a piece of paper on a table.
In order to draw a line on that piece of paper you must put the pen down somewhere and move it in the desired way.
To draw a second line, you must lift the pen, move it where the second line should start, put the pen down and move it again.
Continuing like this you can theoretically draw anything you want on that piece of paper.

Well, this setup i've just described is the foundation for [Turtle graphics](https://en.wikipedia.org/wiki/Turtle_graphics).
Turtle graphics is called like this because there is a virtual turtle which has a position, an orientation and a pen that can be lowered/raised.
The main idea is that to draw something you give the turtle commands, which are relative to the status of the turtle in that moment.
This leads to commands such as "go forwards", "go backwards", "rotate 90° clockwise" and so on.

In Downbeat paths are constructed in a similar way to Turtle graphics, but the syntax used is not "command based" but rather much more similar to the one used in SVG.
Let's touch on them before going into more detail.

### SVG Paths
[SVG](https://en.wikipedia.org/wiki/SVG) is one, if not the most widely used format for vector graphics.
For this introduction we're interested about a particular part of the specification: SVG paths.

MDN's page about [SVG paths](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Paths) describes very well how they work.
In short, SVG paths are an XML element that contain a parameter called `d`, with a string containing various letters and numbers that are interpreted as drawing commands for that path element.
This allows to draw arbitrary shapes which cannot be represented simply by lines, rectangles and ellipses.

The main difference between the commands used for SVG paths and turtle graphics is that in the SVG world there is only a pen with a position, without orientation and the commands can both be relative or absolute.
When they are absolute, the commands specify a precise point in space, while relative commands specify in world units how the pen should move in each axis.
For example, inside an SVG path there can be a command like "move 4 units right, 3 units down", where as in turtle graphics the turtle can only move forwards or backwards.

We highly recommend reading the links provided.
They're very well written and no explaination done here would be clearer than what's already written there.

## Let's start small
At the heart of everything there is a point called **cursor** in space. 
This cursor moves through space using commands, which specify *where* the cursor should move to and *how* it should reach the new point in space.
For now **assume** that space refers to 3d space, there are some cases which this does not hold that will be explained [later](#chronopaths-and-path-names).

The easiest command to understand is the Move command (`M`), which moves the cursor to the new position by "teleporting it".
In the analogy with pen and paper, it is the equivalent of starting with the pen floating in a specific point, then moving it somewhere else while still floating.
As you can probably understand, moving the pen above the paper does not draw anything and in fact this is also true for Downbeat.
Just using Move commands is not enough to "draw" a Path that can be used by the game, so let's introduce another command that actually draws something.

The Linear command (`L`) moves the cursor to a new position and draws a straight line between the old cursor point and the new cursor point, specified by the command itself.

Now that we know these two commands, let's talk a bit about syntax so we can do some examples.

## The syntax of Path commands
The complete syntax of Paths is described in [PathFormat.md](PathFormat.md), but for now let's talk about just the syntax for the two commands introduced.

Every command is written as `<type> <point>`:
- `<type>` is a single letter that is associated with each command type; in the case of the Move command and the Linear command they are respectively `M` and `L`
- `<point>` is the coordinates of the point required for the command (will be referenced [later](#chronopaths-and-path-names))

Here are some examples:
- A Move command that shifts the cursor to the position (1, 3, -4)  would be written as `M 1 3 -4`
- A Linear command that moves the cursor to (-2, 4, 4) would be written as `L -2 4 4`

Of course, in order to make a more complicated path multiple commands are required one after the other.
To do that write the commands one after the other separated by space, for example `M 1 3 -4 L -2 4 4`.

## Curve commands (and how multiple points are handled)
Let's introduce another command, the `Q` command.
This command creates a quadratic bezier curve from the current cursor position A to the destination B, using control point C.
Since this command needs multiple points to work, you just need to write the coordinates of each point, one after the other, separated by spaces (Note: the SVG format requires that a comma is present to separate the points, in Downbeat this is not required).

A real example: `Q 5 8 0 10 0 0` creates a quadratic bezier curve from the cursor to the point (10, 0, 0) using (5, 8, 0) as a control point.

## Timed Paths and how Downbeat evaluates them
Until now Paths were introduced by talking about graphical things such as SVGs.
In reality Paths are used to control various values during gameplay and the method to translate from path to "thing" reveals some key points that need explaination.

Let's make an example:
Consider two paths A and B, where A is `M 0 0 0 L 1 2 3` and B is `M 1 2 3 L 0 0 0`.
In a drawing context like SVG this would result in the same line connecting the points (0, 0, 0) and (1, 2, 3).
In Downbeat they are not the same because commands are evaluated left to right, obtaining **a path that starts in the leftmost command and ends in the rightmost command**.
Getting back to the example, path A *starts* on the point (0, 0, 0) and *ends* on point (1, 2, 3), while path B *starts* on (1, 2, 3) and *ends* on (0, 0, 0).
To explain why this is important it's time to explain what *Timed Paths* are.

Timed Paths are *Paths that have a time value associated with it*.
Each pair is written as `<time> = <Path>`:
- `<time>` is a numeric value that represents the starting time for this Timed Path
- `<Path>` is a collection of one or more Path commands as described in [The syntax of Path commands](#the-syntax-of-path-commands).

So, let's take the paths A and B declared previously and assign them the time values 1 and 2, obtaining `1 = M 0 0 0 L 1 2 3` and `2 = M 1 2 3 L 0 0 0`.
In *most* cases Downbeat will take these Timed Paths and use them to get a value (in these examples a point in 3D space) to set as a parameter for something.
Downbeat has a completely separate time value, called `playback time`, which represents how much time in beats has elapsed since the start of the song.
**`playback time` is used to interpolate between two consecutive timed paths in a linear way between the specified `<time>` values of the timed paths**.

In order to explain exactly how this is done let's return to the previous example, we have `1 = M 0 0 0 L 1 2 3` and `2 = M 1 2 3 L 0 0 0`.
When `playback time` is between 1 and 2, Downbeat will normalize the `playback time` from the range [1,2) into the range [0,1).
For this new normalized value we give the name `p`.
Once calculated `p`, Downbeat will take the timed path associated with 1 and use `p` to calculate the final value to use.
This process is also done by linearly interpolating the `<path>` defined so that **`p = 0` represents the start of the path and `p = 1` represents the end of the path.**

Here is a table where some `playback time` values are mapped to the final value: 

| playback time | p | final value |
|:---:|:---:|:---:|
| 1 | 0 | (0, 0, 0) |
| 1.25 | 0.25 | (0.25, 0.5, 0.75) |
| 1.5 | 0.5 | (0.5, 1, 1.5) |
| 1.75 | 0.75 | (0.75, 1.5, 2.25) |

This table makes it possible to see the difference between the two example timed paths, because if they were inverted (`1 = M 1 2 3 L 0 0 0`, `2 = `M 0 0 0 L 1 2 3`) the resulting table would be:

| playback time | p | final value |
|:---:|:---:|:---:|
| 1 | 0 | (1, 2, 3) |
| 1.25 | 0.25 | (0.75, 1.5, 2.25) |
| 1.5 | 0.5 | (0.5, 1, 1.5) |
| 1.75 | 0.75 | (0.25, 0.5, 0.75) |

### Edge cases
Let's assume that we have the following list of Timed Paths for discussing the edge cases; the Path part of them is not relevant, just the time values associated with them:

```
2 = ...Path A...
5 = ...Path B...
```

*Case 1:* What happens when the playback time `t` has a value below 2?
Since there is no possible time range before 2, what happens is the cursor will still be positioned at the start of Path A, *as if* the `t` value was clamped to not be below 2

*Case 2:* What happens when the playback time `t` has a value above 5?
Same reasoning is applied, the cursor will be placed at the end of Path B, *as if* the time value was clamped to not be above 5.
For this reason the last Timed Path is less useful compared to the others, because the path declared for it will basically be unused except for the end.

*Case 3:* What happens when the list only has one Timed Path?
This case is handled in the same way as Case 2, where only the start will be considered.
There are some cases where you only need to set the cursor position and keep it there forever, in which case a single path is enough to achieve that.

## How paths are handled for notes
TODO

## ChronoPaths and path names
So, now that we know what Paths are, how Timed Paths are defined and all the trouble of how time values are used, there's only one thing left to explain: how does Downbeat in practice use all of this?
Until now we generally said "it's used during gameplay to change various things", now it's time to truly explain what it means.

Most objects during gameplay (Camera, notes, highway, diamonds...) have properties.
These properties are set every frame by calculating where the cursor should be in a path and then taking the coordinates to set the property.

In the specific case of the Camera we have:
- `Camera Position`
- `Camera Look target`
- `Camera Up direction`
- `Camera FOV`

These properties all rely on Paths to get the cursor position, but they use the resulting values in different ways:
- `Camera Position` and `Camera Look target` calculate the position of the cursor and then uses it as a unity world position.
- `Camera Up direction` calculates the position of the cursor and then uses the coordinates to construct a 3d vector that determines the relative up direction of the camera
- `Camera FOV` calculates the position of the cursor and then uses it as a numeric value

...but isn't the cursor 3d? how does it take that and sets it as a numeric value?
No, the cursor is not *always* 3d.

Every property require a different amount of numbers, so properties that refer to a unity world position require 3 values, while the FOV for the camera only requires a single number.

Let's define this more rigorously: a `ChronoPath` is *an object containing a name, a dimention value and a collection of Timed Paths.
For each Timed Path, the Path inside must contain commands that specify points with the same dimention as the one contained inside the ChronoPath Object*.

**In order to set a property, Downbeat will look for a ChronoPath with the same name as the property, with the correct value for the dimention.**
To set `Camera Position` Downbeat will look for a ChronoPath with that name, having dimention equal to 3, while for `Camera FOV` it will look for a ChronoPath having a dimention value of 1.
Because of this, the example paths in [The syntax of Path commands](#the-syntax-of-path-commands), since they define points as 3d, could be contained in the ChronoPath for `Camera Position`, but they couldn't be contained in the one for `Camera FOV`.

Once a ChronoPath is found, the playback time of the current song will be used as `t` value to determine the correct [time range](#timed-paths-and-how-downbeat-evaluates-them) to consider, to finally get the position of the cursor.

