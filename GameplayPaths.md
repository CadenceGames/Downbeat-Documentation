# Gameplay ChronoPaths
All these ChronoPaths follow the format described in [PathFormat.md](PathFormat.md#chronopath)

See [NoteGroups.md](NoteGroups.md) and [Images.md](Images.md) for additional ChronoPaths that are not listed here

These ChronoPaths should only be placed in `<InstDiff>.path`.

|Property | Dimention |  Description|
|:---: | :---: |  :---|
|`VideoOffset` | 1D | defines the video offset that is used as a base for rendering notes and beatlines|
|`Speed` | 1D | sets a speed multiplier that is applied to the current player highway speed. Does nothing if the player has set the speed type to constant|
|---|---|---|
|`Highway.Position` | 3D | Defines the center position of the highway plane|
|`Highway.Rotation` | 3D | Defines the rotation of the highway plane|
|`Highway.Scale` | 3D | Defines the scale of the highway plane (note that only x and y are used) ((should it be 2D instead?))|
|`Highway.Offset` | 1D | Defines the uv offset that is applied on top of `VideoOffset` to get the image scrolling position|
|---|---|---|
|`LeftRail.Follow` | 1D | If set to a value > 0.5, forces the left rail to be placed on the left of the highway, based on its position, rotation and scale|
|`LeftRail.Position` | 3D | Defines the position of the left rail (active only if `LeftRail.Follow` is <= 0.5)|
|`LeftRail.Rotation` | 3D | Defines the rotation of the left rail (active only if `LeftRail.Follow` is <= 0.5)|
|`LeftRail.Scale` | 3D | Defines the scale of the left rail (active only if `LeftRail.Follow` is <= 0.5)|
|---|---|---|
|`RightRail.Follow` | 1D | If set to a value > 0.5, forces the right rail to be placed on the right of the highway, based on its position, rotation and scale|
|`RightRail.Position` | 3D | Defines the position of the right rail (active only if `RightRail.Follow` <= 0.5)|
|`RightRail.Rotation` | 3D | Defines the rotation of the right rail (active only if `RightRail.Follow` <= 0.5)|
|`RightRail.Scale` | 3D | Defines the scale of the right rail (active only if `RightRail.Follow` <= 0.5)|
|---|---|---|
|`LeftMeter.Follow` | 1D | If set to a value > 0.5, forces the left meter to be placed relative to the left side rail, based on its position, rotation and scale|
|`LeftMeter.Position` | 3D | Defines the position of the left meter (active only if `LeftMeter.Follow` is <= 0.5)|
|`LeftMeter.Rotation` | 3D | Defines the rotation of the left meter (active only if `LeftMeter.Follow` is <= 0.5)|
|`LeftMeter.Scale` | 3D | Defines the scale of the left meter (active only if `LeftMeter.Follow` is <= 0.5)|
|---|---|---|
|`RightMeter.Follow` | 1D | If set to a value > 0.5, forces the right rail to be placed relative to the right side rail, based on its position, rotation and scale|
|`RightMeter.Position` | 3D | Defines the position of the right meter (active only if `RightMeter.Follow` <= 0.5)|
|`RightMeter.Rotation` | 3D | Defines the rotation of the right meter (active only if `RightMeter.Follow` <= 0.5)|
|`RightMeter.Scale` | 3D | Defines the scale of the right meter (active only if `RightMeter.Follow` <= 0.5)|
|---|---|---|
|`Camera.Position` | 3D | Defines the position of the camera that is rendering the highway.|
|`Camera.Look` | 3D | Defines the virtual point where the camera points to.|
|`Camera.Up` | 3D | Defines where the up vector of the world is pointing; used for rolling the camera.|
|`Camera.FOV` | 1D | controls the Field Of View of the camera responsible to rendering the higway to the player|
|---|---|---|
|`Beatlines.Position` | 3D | Defines the path beatlines must follow.|
|`Beatlines.Easing` | 3D | Defines the easing function used for interpolating from time value to position for beatlines.|
|---|---|---|
|`Striker.Position` | 3D | Defines where the middle fret of the striker is placed in the world.|
|`Striker.Rotation` | 3D | Defines the rotation in euler angles of the strikers.|
|---|---|---|
|`DiamondAccuracy.Position` | 3D | Defines where the diamond is positioned in world space|
|`DiamondAccuracy.Rotation` | 3D | Defines how the diamond is rotated, in euler angles|
|`DiamondAccuracy.Scale` | 3D | Defines what is the scale of the diamond|
|`DiamondAccuracy.Follow` | 1D | Defines if the diamond should be aligned with the camera|
|---|---|---|
|`DiamondCombo.Position` | 3D | Defines where the diamond is positioned in world space|
|`DiamondCombo.Rotation` | 3D | Defines how the diamond is rotated, in euler angles|
|`DiamondCombo.Scale` | 3D | Defines what is the scale of the diamond|
|`DiamondCombo.Follow` | 1D | Defines if the diamond should be aligned with the camera|
