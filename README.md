# Downbeat Documentation Index
Welcome to the Index page for all the documentation available for Downbeat.
This guide is updated as of 2026-02-16

## Main Pages
- [HowPathsWork.md](HowPathsWorks.md): Explains the concept of paths and explains how they're used
- [PathFormat.md](PathFormat.md) : Explains what a Path file is and how it's structured
- [NoteGroups.md](NoteGroups.md) : Explains the concept of Note Groups and how they can be used for manipulating notes
- [Images.md](Images.md) : Explains how images can be embedded in the gameplay and how to manipulate them at runtime
- [Triggers.md](Triggers.md) : Explains what triggers are and how they are interpreted by Downbeat
- [GameplayPaths.md](GameplayPaths.md) : Lists all paths that are related to general gameplay and are not specific to notes or images
- [EditorShortcuts.md](EditorShortcuts.md) : Lists all default shortcuts for the Modchart editor
- [AssetBundles.md](AssetBundles.md): Explains how to create and load Assetbundles when playing a song

## A quick note about parameters
In order to explain how things work, these documentaion pages might contain some kind of syntax dictating what, where and how to write specific lines.
In all linked pages, the syntax will be described like this:
- `text` represent literal text that must be written as is
- `<foo>` represent a required argument, called `foo`, that must be replaced with a valid argument without including `< >`
  - if the argument is `<DiffInst>`, that argument must follow the same naming scheme used for [sections in .chart files](https://github.com/TheNathannator/GuitarGame_ChartFormats/tree/main/doc/FileFormats/.chart)
- `[<bar>]` represent an optional argument, called `bar`, that can be replaced with a valid argument without including `[ ]`
- If an argument is followed by `+`, that means that argument can be repeated multiple times.


