---
description: ' Use the First Focus Element property to determine which element receives
  first focus when a canvas is first loaded in O3DE. '
title: First Focus Element
---

To determine which element receives first focus when a canvas is first loaded, set the **First Focus Element** in the [Canvas Properties](/docs/user-guide/interactivity/user-interface/editor/canvas-properties.md). The **First Focus Element** receives focus upon canvas load when no mouse is detected. If you do not set a **First Focus Element**, or if a mouse is detected, no element has focus until the user provides direction input from a keyboard or mouse. At that point, the element closest to the top left corner of the canvas receives focus.

Once an element has focus, navigation to other elements is controlled by the [navigation properties](/docs/user-guide/interactivity/user-interface/editor/components-interactive-properties-navigation.md) defined for each interactive component.
