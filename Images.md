# Images
## Overview
Downbeat has support for loading images at runtime that will be placed in the same space as the player highway.
The loading of images is based on two files, which have to be placed inside the folder of the song:
- `imagesDictionary.ini` : Also called "Dictionary File", this file lists what images should be created and assigns them unique identifier strings.
- `<DiffInst>Images.path` : This file contains all ChronoPaths used to manipulate the images once they are created. It follows the same format described in [PathFormat.md](PathFormat.md)

In order to explain how this mechanism works, let's define two concepts:
- `Image Source` : This is a standard image file (jpeg,png) which contains the actual pixel data. 
- `Image Object` : This is a plane existing in the Unity scene that displays the contents of an Image Source during gameplay.

## Image Dictionary File
This is a standard [ini file](https://en.wikipedia.org/wiki/INI_file#Format) that is used to list all images that should be loaded.
In this file every ini section contains a valid `<DiffInst>` combination that the player might choose.
Below this header follows a list of `key=path` pairs.
Each pair will create an Image Object with id `key` that references an Image Source located at `path`.
`path` contains a relative path based on the folder that contains `imagesDictionary.ini` (so the folder of the song itself).

### Example
```ini
[ExpertSingle]
foo = path/to/image.png
foo2 = path/to/image.png
bar = path/to/anotherImage.jpg
```
In this example, when the player chooses Expert Guitar three image objects will be created with id `foo`, `foo2` and `bar`.
The first two reference the same image source, `path/to/image.png`, while the third image object references another completely different image source.

## Image ChronoPaths
All of these paths must be placed in `<DiffInst>Images.path`

|Property | Dimentions | Description|
|:---: | :---: | :---|
|`<imageID>.Position` | 3D | Defines the path used for the position of the image with the specified id.|
|`<imageID>.Rotation` | 3D | Defines the rotation applied to the image. The Vector components represent the degrees to rotate in that axis. For more information see [Quaternion.Euler](https://docs.unity3d.com/ScriptReference/Quaternion.Euler.html).|
|`<imageID>.Scale` | 3D | Defines the scale factor that is applied to all axes of the image.|
|`<imageID>.Tint` | 4D | Defines the tint color of the image. The components of the vector are interpreted as RBGA values, from 0.0 to 1.0.|
|`<imageID>.Billboard>` | 1D | If set to a value > 0.5, it forces the rotation of the image so it looks flat from the gameplay camera|

- `imageID` : string indentifier for an image, as defined in the dictionary file
