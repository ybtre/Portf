---
author: "Ritschie"
title: "OpenGL Scene"
date: 2020-12-18T12:00:06+09:00
description: "First experience using OpenGL and the Render Pipeline"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: Hristo Vuchev
authorEmoji: 👻
image: /PostImages/opengl.png
tags: 
- opengl
- cpp
categories:
- university
- coursework
series:
- University Projects
---
With this course we got introduced to OpenGL, or more specifically GLUT. The goal of this course and project was to design and develop a 3D graphics application and scene that exhibits key techniques in graphics programming using OpenGL. 
  
The techniques I have successfuly implemented are:
  
##### Lighting 
- Point light and a spot light
- different properties on the geometry to simulate different reflection values and materials


##### Geometry 
- hand crafted geometry using vertexes
- transparent textures
- depth buffer for the skybox
- hierarchy modelling for planets orbiting a sun
- trilinear filtering for the ground texture 
 
##### Camera
- fully functioning 1st person camera with keyboard and mouse movement
- static birds eye camera
</br>

This was an amazing learning experience. I had not used an API before and it was interesting to learn how the render pipeline works. The biggest challenge I faced in this project was the calculations for the camera, but with the help of the teaching staff and fellow students I got it working as I wanted and understood much better how the math behind it works. 