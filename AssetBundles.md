# Asset Bundles loading
When initially loading the song, Downbeat will look for the following files:
- `main-windows.dbab`: actual AssetBundle file, loaded if the game is running on Windows
- `main-linux.dbab`: actual AssetBundle file, loaded if the game is running on Linux
- `<DiffInst>GameObjects.path`: optional file that contains paths for the object inside the AssetBundle

If you're making a modchart it is ***highly*** recomended to put both `main-windows.dbab` and `main-linux.dbab` inside the folder for a song.

If Downbeat successfully finds the AssetBundle, it will load it and instantiate it in the scene, centered at the world origin with no rotation and scale of 1 in every axis.
If the `<DiffInst>GameObjects.path` file is also present, Downbeat will load it in the same way as other path files and link it with the object instantiated from the AssetBundle

## Creating a valid AssetBundle using the template
A template Unity project, available at [https://github.com/CadenceGames/Downbeat-AssetBundle-Template](https://github.com/CadenceGames/Downbeat-AssetBundle-Template), is set up with everything you need to create valid AssetBundles.

### Initial Setup
Download the sources by either clicking on "Code" -> "Download ZIP" and then extracting the files or, if you already know how to use git, with `git clone`.
Once everything is downloaded, install and set up UnityHub if not present so that you can see the "Project" tab (Unity requires making an account and having at least a "Personal" licence).
Click "Projects" -> "Add" -> "Add project from disk" -> open the folder where you have the files.
If done successfully you should see "Downbeat-AssetBundle-Template" in the list.
At this point just click on it, if you don't have the specific Unity editor version it should prompt you to install it before opening the project.

### Building the AssetBundles
The project contains a prefab called `Root` under `Assets`.
Everything that is contained inside this prefab, `Root` object included, will be added to the built AssetBundle.
In order to build, select "Assets" -> "Build AssetBundles".
Once this build step has been completed, you should have `main-windows.dbab` and `main-linux.dbab` under `Assets/Output`.
Just copy these files in the song folder and you're done.

## API Reference
The template contains additional scripts under `Assets/Scripts` that are used by Downbeat to provide additional functionality when loading AssetBundles and for the single object inside it.

### GameObjectMover
This MonoBehaviour class is used to control the local position, rotation and scale of an object.
In order to work it requires having `<DiffInst>GameObject.path` in the song folder and setting an `id` in the inspector.
If done correctly, GameObjectMover will used the following paths to control the transform of the object:

| Property | Dimention | Description | 
| :--- | :---: | :--- |
| `<id>.LocalPosition` | 3D | Controls the [local position of the object](https://docs.unity3d.com/6000.0/Documentation/ScriptReference/Transform-localPosition.html) |
| `<id>.LocalRotation` | 3D | Controls the [local rotation of the object](https://docs.unity3d.com/6000.0/Documentation/ScriptReference/Transform-localRotation.html), in degrees |
| `<id>.LocalScale` | 3D | Controls the [local scale of the object](https://docs.unity3d.com/6000.0/Documentation/ScriptReference/Transform-localScale.html) |



