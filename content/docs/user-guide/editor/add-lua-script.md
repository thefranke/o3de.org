---
description: ' Use the Lua Script component to add script functionality to your game
  entities in Open 3D Engine. '
title: Adding Lua Scripts to Component Entities
---

O3DE makes it easy for you to add script functionality to your game entities by using the **Lua Script** component. The following steps show you how to do this in O3DE Editor.

**To add a Lua script to a component entity in O3DE Editor**

1. With the **Entity Inspector** view pane visible, select the entity in the viewport.

1. Click **Add Component**, and then open **Scripting**, **Lua Script**.
![\[Click Add Component\]](/images/user-guide/scripting/lua/lua-scripting-ces-1.png)

1. Scroll down to the **Scripting** section, and then click **Lua Script**.
![\[Click Lua Script\]](/images/user-guide/scripting/lua/lua-scripting-ces-2.png)

1. A **Lua Script** component appears in the inspector. Use the file selection button (**...**) to select the Lua script from the file hierarchy that you want to use.
![\[Lua Script component\]](/images/user-guide/scripting/lua/lua-scripting-ces-3.png)

   You can select either a `.lua` file (a text copy of the original), or a `.luac` file (a precompiled version of the script). The functionality should be the same. The precompiled version is preferable because it loads faster and is usually smaller. However, you can use`.lua` files if you experience any issues.

1. After the script is loaded, click **Edit Script** (**\{\}**) to launch the Lua IDE and make changes to your script.
