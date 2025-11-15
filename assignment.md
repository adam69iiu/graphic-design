Assignment 1: Static 2D and 3D Scenes
CE307 Computer Graphics, UGIII SE
Mustafa Mustafa, PhD
This assignment should be completed after studying the provided lectures until the end
of the Sierpi´nski triangle example. The goal is to create one static 2D image and one
static 3D image using WebGL/Three.js. No animation or user interaction is required.
The focus is on building and displaying geometry through the computer graphics pipeline.
Both tasks must produce high-resolution images that show clear shapes, balanced colors,
and correct use of the viewing area. Students are expected to apply what they have
learned about scene, camera, rendering, vertices, and colors.
Part 1: Static 2D Scene
Objective: Create a two–dimensional composition using triangles or lines in clip space.
Requirements:
• Use at least twelve triangles or an equal number of line segments.
• Each vertex should have color information so that color blending appears across
the shape.
• Draw the image once using a single call to drawArrays.
• The program should display the image and stop (no looping or motion).
• The final resolution should be at least 1920×1080 pixels.
Suggested ideas:
• A finite version of the Sierpi´nski triangle with color per level.
• A group of triangles arranged into a simple geometric logo or pattern.
• Concentric triangle rings around a center point.
• A regular triangular tiling or grid with color gradients.
• A star or flower made from several triangles.
Save the final rendered result as a PNG image from the canvas.
1
Part 2: Static 3D Scene
Objective: Build a simple three–dimensional scene that includes one object, one light
source, and a camera.
Requirements:
• Create one 3D shape such as a cube, sphere, or pyramid.
• Add at least one light source to show surface shading.
• Use a basic material (for example a diffuse or Phong material).
• Position the camera so the object is centered and clearly visible.
• Render the scene once and save the image as a PNG.
• The image should be at least 2560×1440 pixels.
Optional (you will get bonus marks):
• Add a simple flat ground or background color for context.
• Add a small shadow or reflection if supported.
Include a short note that lists:
• The camera position and projection type.
• The type and position of the light source.
• The material and color used for the object.
Expected Outcomes
By finishing this assignment, you will:
• Learn how to describe geometry using vertices, colors, and coordinates.
• Understand how data is passed through the graphics pipeline to the GPU.
• Practice building 2D and 3D scenes directly from code rather than using prebuilt
models.
• Strengthen your understanding of how real–time graphics are generated at a low
level.
2
This assignment is different from creating an image in GUI-based tools such as 3ds Max
or Blender. In those programs, most of the work is hidden (the software handles the
math and rendering automatically). Here, you are writing the code that defines the
geometry, passes it to the GPU, and draws it using WebGL/Three.js. This gives you
direct experience with the same process used in modern graphics systems.
Quality Guidelines
• Use a large canvas size and enable antialiasing if possible.
• Keep shapes fully inside the visible area and avoid clipping.
• Use simple, balanced colors for clarity.
Submission Instructions
Submit the following:
• One PNG image for the 2D scene.
• One PNG image for the 3D scene.
• The complete source code for both parts.
• A short word/text file explaining the code part by part.
3
