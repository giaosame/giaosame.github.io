---
title: "Mini Maya"
permalink: /projects/mini_maya
---

Mini Maya is a mesh editor application in the style of [Autodesk Maya](https://www.autodesk.com/products/maya/overview), developed in Qt with C++ and OpenGL.

## Features
- Graphical User Interface  
Based on Qt widgets, users can select a certain vertex, half-edge or face by selecting widget items on Qt interface, the corresponding mesh components will be highlighted with special color in GUI. In addition, based on ray casting, users can directly click a certain mesh component to select it.
- Topology Editing Functions  
1) Add a vertex as the midpoint of the currently selected half-edge.  
2) Triangulate a selected face.  
3) Extrude a selected face along its geometric surface normal.  
4) Smooth a given polygon using Catmull-Clark subdivision.   
![Mini Maya Pic1: topology editing](/assets/images/projects/mini_maya/topology_editing.gif)
&nbsp;

- The OBJ File and JSON File Importing
![Mini Maya Pic2: objects loading](/assets/images/projects/mini_maya/obj_loading.png)
&nbsp;

- Sharpness  
Allow the user to "sharp" specific faces, edges, and vertices during subdividing a mesh, also the user can set a "sharpness" value for any mesh component that has been tagged as sharp.  
![Mini Maya Pic3: sharpness](/assets/images/projects/mini_maya/sharpness.gif)
&nbsp;

- Shader-based skin deformation  
Transform vertices based on the joints that influence them, deform the skin with the naive distance-based skinning function, the heat-diffusion style skinning function and the dual-quaternion skinning, respectively.
![Mini Maya Pic4: heat-diffusion style skinning](/assets/images/projects/mini_maya/skinning.gif)  
