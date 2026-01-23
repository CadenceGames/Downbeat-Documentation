# Note Groups and Paths
## Overview
Downbeat has the ability to set the position, scale and rotation of any note in the chart.
The system used to do that does **not** directly set the values for each note, but uses a layer of abstraction using `Note Groups`.

Note groups are collections of notes that have a common name.
That name is then used to calculate the position, rotation and scale of every note inside it.
Since the name is the same for every note in the group, this system allows you to specify how *one* note in the group should transform and it will be applied in the same way to every other note in the group, making effects that require multiple notes much easier.

Please refer to [A quick note about parameters](README.md#a-quick-note-about-parameters) regarding how the syntax is described in this file.

## Note Group
You can create Note groups in two ways: by selecting a time range where all notes inside are included in the group or by adding each note individually.

If a note is not assigned manually to a group, it will be assigned automatically to the "*automatic*" group.
**For this reason "automatic" can but _should never_ be used as a group name**.
Meanwhile `Strikers` is a reserved word and **cannot** be used as a group name, at all (`Strikers.Lane#` is interpreted as a path used for controlling strikers, not for controlling notes).


In order to create a note group you must add text events with a special syntax to the chart itself (in all three cases, `name` is a required argument):
| Event | Example | Description | 
| :---|:---|:--- | 
| `group:<name>` | `group:foo` | Defines the start of a time range used to group notes into the group called `name`. | 
| `groupend:<name>` | `groupend:foo` | Defines the end of a time range used to group notes into the group called `name`. | 
|`groupadd:<name>` | `groupadd:bar` | Adds the note with the same tick value to the note group specified by `name`. | 

In order for a time range to be valid, both the start and end event must have the same name and the end must be on a later tick than the start tick.
Suppose we have a start event at tick A and an end event on tick B, the time range selects notes in the interval `[A,B)`, that is the note at tick A is included but the note at tick B is not.

Nesting of note groups is not allowed, because notes are limited to one group only.
You can include notes that are not consecutive to the same group, by using the add command or by using multiple time ranges with the same name.

## Note ChronoPaths
These ChronoPath names follow the format described in [PathFormat.md](PathFormat.md)
Downbeat loads note paths only for the instrument and difficulty that the player has selected. To do this, it will attempt to load the file called `<DiffInst>.path`.


|Property | Dimention | Description|
|:--- | :---: | :---|
|`<noteGroup>.Lane<i>.Position` | 3D | Defines the path that notes in that lane must follow.|
|`<noteGroup>.Lane<i>.PositionBlending` | 1D | Defines the blending curve used to calculate the position path used for note position.|
|`<noteGroup>.Lane<i>.PositionEasing` | 1D | Defines the easing curve to change how fast notes should move along the position path.|
|`<noteGroup>.Lane<i>.Rotation` | 3D | Defines the rotation of the note along the path in euler angles.|
|`<noteGroup>.Lane<i>.RotationBlending` | 1D | Defines the blending curve to calculate the rotation path used for note rotation.|
|`<noteGroup>.Lane<i>.RotationEasing` | 1D | Defines the easing curve to change how fast notes should rotate along the rotation path.|
|`<noteGroup>.Lane<i>.Scale` | 3D | Defines the scale of the note along the path.|
|`<noteGroup>.Lane<i>.ScaleBlending` | 1D | Defines the blending function used to calculate the scale path used for note scale.|
|`<noteGroup>.Lane<i>.ScaleEasing` | 1D | Defines the easing curve to change how fast notes should scale along the scale path.|
|`<noteGroup>.Offset` | 1D | Defines the visual offset of this note group in beats, relative to the normal position of the notes in the chart|
|`<noteGroup>.Visible` | 1D | Defines if the entire note group should be rendered in the first place. Notes are visible if this path is > 0.5|

- `noteGroup` : name of the target note group that you want to set the path for
- `i` : numeric value that is used to target only notes that are part of the same lane. See the table below for what numeric values are accepted for each game mode.

### Available lanes
|Instrument | Lane0  | Lane1  | Lane2  | Lane3  | Lane4  |   Lane5|
|   :---:   | :---:  | :---:  | :---:  | :---:  | :---:  |   :---:|
|5-Fret Mode| Open Note | Fret 1 | Fret 2 | Fret 3 | Fret 4 | Fret 5|
