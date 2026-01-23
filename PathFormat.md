# Path Format (.path)
## Overview
The goal of this markdown file is to describe what a Path file is

Downbeat uses a special file type to determine values that can be used for all sorts of things, such as note positions, camera's field of view or how fast should the notes reach the player.
This file is just a regular text file encoded in UTF-8.

Please refer to [A quick note about parameters](README.md#a-quick-note-about-parameters) regarding how the syntax is described in this file.

## File Syntax
The file itself is just a list of one or more objects, each one of type ChronoPath and that are written with their specific [syntax](#chronopath).

### Comments
The Path Format also allows comments to be written inside the file. Comments are defined in a very similar way to how the C language defines them, using slashes:
- `//` : single line comment, can be put on any line and disables the interpretation of everything that appear on the right of this symbol
- `/*` and `*/` : multi line comment, disables the interpretation of every line from `/*` to `*/`. **These symbols can only be placed at the start of the line with nothing else afterwards**

## ChronoPath
ChronoPath objects are the main type of object that a Path File contains. They are used to create piecewise functions from a time value to a vector with n coordinates.

In order to do this, the ChronoPath has associated with it a dimention value (defined in the Identifier), used to determine how many dimention the output vector must have, and a list of [Paths](#path-syntax).
These paths are set up like keyframes on a timeline, where at specific time values there are path objects assigned to them. For time values in between, interpolation is used to calculate the resulting vector from the path.

### Main syntax
```
[<Tag>]
<Identifier>
<Data Block>
```
Each section must be placed on a separate line from the others.
The Tag section is the only one that is *not required*.
These sections must be placed in this specified order

### Tags Syntax
```
(<tag>)
```
- `tag` : string value to assign as a tag to this ChronoPath

### Identifier Syntax
```
[Vec<dimention>:<name>]
```
**NOTE: The square brackets around are literal [ ]. They are not used to mark an optional argument**
- `dimention` : integer value that is used as the dimention value for this ChronoPath
- `name` : string value that is used as the name for this ChronoPath

### Data Block Syntax
```
{
    <data entry>
    [<data entry>]+
}
```
There must be at least one `data entry` inside a ChronoPath and the tab before the data entry is not required (but if you're writing the file manually is highly encouraged)

Data entries follow this format
```
<time> = <path>
```
- `time` : floating point value used as a time marker, before dividing.
- `path` : a path written as described in the [path syntax](#path-syntax)

The time value is first divided by a `resolution` value and then the resulting floating point value is treated as a time value in beats from the start of the song.
By default a Path file has a resolution value of **192** and this is done in order to match the default resolution of moonscraper for the distance between two beats.

This resolution value can be changed using the command
```
#Resolution <value>
```
on a single line. This will set the resolution value from that line going down to that specified value. Only positive values can be used for resolution.

## Path Syntax
This is the lowest level of data available in a Path file and is the core of everything.
Paths represent a curve in space that is composed of one or more segments that are joined together into one, drawing the path in a similar way to [turtle graphics](https://en.wikipedia.org/wiki/Turtle_graphics).

The syntax for Paths is very much inspired by the SVG standard for vector images [MDN web page explaining the SVG path format](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Paths).

### Segment Syntax
Each path is a sequence of segments, each one composed of a type followed by a variable number of floating point values.

Just like SVG, types of segments are defined using single letters which are either uppercase or lowercase. If the letter is uppercase, the values following the command are interpreted as absolute values. If the letter is lowercase, the values following the command are interpreted as movement from the last position

The number of floating point required for a command is based both on the type and on the dimention of the ChronoPath containing this Path.
In the following table, the number of values required is defined after the name of the parameter, separated by a colon.If a parameter has `N` values, that means the command requires as many floating point values as the dimention specified in the ChronoPath, otherwise if it's a number that means the command always require that number of parameters

|Letter | Command | Parameters | Description|
|:---: | :---: | :--- | :---|
|M/m | Move Command | `<target:N>` | Moves the cursor to the `target` given, without making a path|
|L/l | Line Command | `<target:N>` | Adds a linear segment between the previous location and the specified `target`|
|K/k | Constant Command | `<value:N>` | Adds a linear segment where both start and end values are equal to the provided `value`.|
|Q/q | Quadratic Bezier | `<control:N>`<br>`<end:N>` | Adds a quadratic bezier segment using the cursor position, `control` and `end`.|
|C/c | Cubic Bezier | `<control1:N>`<br>`<control2:N>`<br>`<end:N>` | Adds a cubic bezier segment using the cursor, `control1`, `control2` and `end`|
|T/t | Continue with Quadratic Bezier | `<target:N>` | If the previous command was `Q/q` or `T/t`, this command adds a quadratic bezier segment calculating the control point from the previous command and using `target` as end. <br> Otherwise, this command adds a linear segment from cursor to `target`|
|S/s | Continue with Cubic Bezier | `<control:N>`<br>`<end:N>` | If the previous command was `C/c` or `S/s`, this command adds a cubic bezier calculating control point 1 from the previous command and using `control` as control point 2 and `end`. <br> Otherwise, this command adds a quadratic bezier curve using the cursor, `control` and `end`|
|A/a | Arc Command | `<center:N>`<br>`<right:N>`<br>`<up:N>`<br>`<startAngle:1>`<br>`<endAngle:1>` | Adds an Arc of an ellipse centered at `center` and having `right` and `up` as the reference vectors. <br> `startAngle` and `endAngle` are single floating point values that specify the angle in radians of the arc.|
|D   | Default Command |  | Sets the cursor to a default position defined by the name used for the ChronoPath that is containing this path|

[For a better explaination of how the S and T commands work](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Paths#b%C3%A9zier_curves)

## Putting Everything Together
Here is a complete example that summarises everything described until now
```
[Vec3:pathOne]
{
    0 = M 0 0 0 L 1 2 3
}

(taggedPath)
[Vec1:pathTwo]
{
    0 = K 0
    192 = M 1 l -1
}
```
