---
description: ' Learn how Open 3D Engine supports cinematics and scripted event sequences. '
linktitle: Cinematics
title: Create cinematic sequences
---

Cinematics, also known as sequences or cutscenes, are interactive movie animations with time-dependent control over objects and events. You can use O3DE to add cutscenes to your game.

You can also add scripted events so that a sequence of objects, animations, and sounds are triggered in the game. The player can view these sequences from their own (first person) or another's (third person) perspective.

Sequences consist of the following elements (listed in hierarchical order), which are created and managed from the Track View:
+ **Node** - Each sequence comprises a top-level director (scene) node, one or more camera nodes, image effects nodes, and entity nodes.
+ **Track** - Depending on the type, each node consists of multiple tracks, such as position, animation, sound, lighting, text, and events. Tracks are displayed in the track timeline pane.
+ **Key** - A key is a setting for a property at a specific time. As the sequence plays, keys are interpolated based on their in and out tangent values set in the Track View.

**Topics**
+ [Using the Track View Editor](/docs/user-guide/visualization/cinematics/track-view/editor.md)
+ [Populating a Scene](/docs/user-guide/visualization/cinematics/populating-a-scene.md)
+ [Animating Characters in Scenes](/docs/user-guide/visualization/cinematics/animation-intro.md)
+ [Capturing Image Frames](/docs/user-guide/visualization/cinematics/image-capture.md)
+ [Debugging Cinematic Scenes with Console Variables](/docs/user-guide/visualization/cinematics/debugging.md)
