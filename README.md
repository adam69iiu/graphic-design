# CE307 Computer Graphics Assignment 1 - Documentation

## Student Information
**Assignment:** Static 2D and 3D Scenes  
**Course:** CE307 Computer Graphics, UGIII SE  
**Instructor:** Mustafa Mustafa, PhD

---

## Part 1: Static 2D Scene - Geometric Flower Pattern

### Overview
The 2D scene creates a geometric flower pattern using 12 triangular petals arranged in a circular formation. Each petal is rendered as a triangle with color interpolation from outer to inner vertices, creating a vibrant rainbow gradient effect.

### Technical Specifications
- **Resolution:** 1920×1080 pixels
- **Number of Triangles:** 12
- **Total Vertices:** 36 (12 triangles × 3 vertices each)
- **Rendering Method:** Single `drawArrays` call
- **Color Space:** RGB with alpha channel

### Code Structure Explanation

#### 1. Shader Programs
**Vertex Shader:**
```glsl
attribute vec4 aVertexPosition;  // Receives vertex position
attribute vec4 aVertexColor;      // Receives vertex color
varying lowp vec4 vColor;         // Passes color to fragment shader

void main(void) {
    gl_Position = aVertexPosition;
    vColor = aVertexColor;
}
```
- Takes vertex position and color as input attributes
- Passes color to fragment shader for interpolation
- Sets vertex position directly in clip space coordinates (-1 to 1)

**Fragment Shader:**
```glsl
varying lowp vec4 vColor;  // Receives interpolated color

void main(void) {
    gl_FragColor = vColor;  // Sets pixel color
}
```
- Receives interpolated color values between vertices
- Outputs final pixel color creating smooth gradients

#### 2. WebGL Context Initialization
```javascript
const gl = canvas.getContext('webgl', { 
    antialias: true,              // Smooth edges
    preserveDrawingBuffer: true   // Keep image for saving
});
```
- Enables antialiasing for smoother rendering
- Preserves drawing buffer to allow PNG export

#### 3. Geometry Generation (`createFlowerGeometry`)
This function generates the flower pattern algorithmically:

```javascript
const numPetals = 12;
const outerRadius = 0.7;  // Outer tips of petals
const innerRadius = 0.3;  // Inner circle radius
```

**For each petal (12 iterations):**
1. Calculate angles for petal boundaries
2. Compute outer point (petal tip) using polar coordinates
3. Compute two inner points where petal meets center
4. Create triangle with these three vertices
5. Assign gradient colors based on position in circle (0-360° hue)

**Coordinate Conversion:**
```javascript
const outerX = centerX + Math.cos(midAngle) * outerRadius;
const outerY = centerY + Math.sin(midAngle) * outerRadius;
```
- Converts polar coordinates (angle, radius) to Cartesian (x, y)
- Places vertices in clip space coordinates

#### 4. Color Generation
Uses HSL to RGB conversion for rainbow effect:
- Each petal gets a hue from 0° to 360°
- Outer vertices: slightly darker (lightness 0.6)
- Inner vertices: brighter (lightness 0.8)
- Creates natural color gradient within each petal

#### 5. Buffer Management
**Vertex Buffer:**
```javascript
const positionBuffer = gl.createBuffer();
gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(geometry.vertices), gl.STATIC_DRAW);
```
- Creates GPU buffer for vertex positions
- Uploads vertex data as Float32Array
- Uses STATIC_DRAW since data won't change

**Color Buffer:**
- Similar process for color data
- Separate buffer allows independent vertex attributes

#### 6. Rendering Pipeline
```javascript
gl.useProgram(shaderProgram);                    // Activate shader
gl.vertexAttribPointer(vertexPosition, ...);     // Connect position buffer
gl.vertexAttribPointer(vertexColor, ...);        // Connect color buffer
gl.drawArrays(gl.TRIANGLES, 0, 36);              // Draw all triangles
```
- Single draw call renders all 12 triangles
- GPU interpolates colors between vertices automatically
- No animation loop - renders once and stops

### Graphics Pipeline Flow
1. **Vertex Data:** JavaScript arrays → GPU buffers
2. **Vertex Shader:** Processes each vertex position and color
3. **Rasterization:** Converts triangles to pixels
4. **Fragment Shader:** Colors each pixel with interpolated color
5. **Frame Buffer:** Final image displayed on canvas

---

## Part 2: Static 3D Scene - Illuminated Sphere

### Overview
The 3D scene features a blue sphere with Phong shading, sitting on a ground plane with realistic shadows. Multiple light sources create depth and dimension.

### Technical Specifications
- **Resolution:** 2560×1440 pixels
- **3D Library:** Three.js (r128)
- **Projection:** Perspective
- **Shading Model:** Phong

### Scene Components

#### 1. Camera Setup
```javascript
const camera = new THREE.PerspectiveCamera(45, 2560/1440, 0.1, 1000);
camera.position.set(5, 4, 8);
camera.lookAt(0, 0, 0);
```

**Parameters:**
- **Type:** Perspective projection (mimics human eye)
- **Field of View:** 45° (natural viewing angle)
- **Aspect Ratio:** 16:9 (2560÷1440)
- **Position:** (5, 4, 8) - elevated and offset for good view angle
- **Target:** (0, 0, 0) - looking at scene center
- **Near/Far Planes:** 0.1 to 1000 units (defines visible range)

#### 2. Renderer Configuration
```javascript
const renderer = new THREE.WebGLRenderer({ 
    antialias: true,              // Smooth edges
    preserveDrawingBuffer: true   // Enable PNG export
});
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
```

**Features:**
- High-quality antialiasing
- Soft shadow mapping (PCF = Percentage Closer Filtering)
- Preserved buffer for image download

#### 3. Main Object - Sphere
```javascript
const sphereGeometry = new THREE.SphereGeometry(2, 64, 64);
const sphereMaterial = new THREE.MeshPhongMaterial({
    color: 0x2196F3,      // Deep blue
    shininess: 80,         // Glossy surface
    specular: 0x444444    // Gray specular highlights
});
```

**Geometry:**
- Radius: 2 units
- Segments: 64×64 (high detail for smooth appearance)
- Position: (0, 2, 0) - elevated above ground

**Material (Phong):**
- **Ambient:** Responds to ambient light
- **Diffuse:** Main color based on light angle (Lambertian reflection)
- **Specular:** Shiny highlights from direct light
- **Shininess:** 80 (high value = tight, glossy highlights)

#### 4. Ground Plane
```javascript
const groundGeometry = new THREE.PlaneGeometry(20, 20);
const groundMaterial = new THREE.MeshPhongMaterial({
    color: 0x3a5f3a,      // Forest green
    side: THREE.DoubleSide
});
ground.rotation.x = -Math.PI / 2;  // Rotate horizontal
```

**Purpose:**
- Provides context and reference
- Receives shadows from sphere
- 20×20 units (large enough to frame scene)

#### 5. Lighting System

**Ambient Light:**
```javascript
const ambientLight = new THREE.AmbientLight(0x404040, 0.5);
```
- Provides base illumination (no direction)
- Prevents completely dark areas
- Gray color at low intensity

**Directional Light (Main):**
```javascript
const directionalLight = new THREE.DirectionalLight(0xffffff, 1.2);
directionalLight.position.set(5, 10, 7.5);
directionalLight.castShadow = true;
```
- **Type:** Directional (parallel rays like sunlight)
- **Position:** (5, 10, 7.5) - above and to the side
- **Color:** White (0xffffff)
- **Intensity:** 1.2 (bright)
- **Shadow Map:** 2048×2048 resolution for sharp shadows

**Hemisphere Light:**
```javascript
const hemisphereLight = new THREE.HemisphereLight(0xffffff, 0x444444, 0.3);
```
- Sky color (top): White
- Ground color (bottom): Dark gray
- Adds subtle environmental lighting

#### 6. Shadow Configuration
```javascript
directionalLight.shadow.mapSize.width = 2048;
directionalLight.shadow.camera.left = -10;
directionalLight.shadow.camera.right = 10;
// ... more shadow camera bounds
```

**Shadow Mapping Process:**
1. Render scene from light's perspective
2. Store depth information in shadow map texture
3. During main render, compare pixel depth with shadow map
4. Darken pixels that are occluded from light

### Rendering Process

#### Three.js Pipeline (Simplified):
1. **Scene Graph:** Organize objects hierarchically
2. **Camera Transform:** Convert world coordinates to view space
3. **Projection:** Apply perspective transformation
4. **Lighting Calculations:** Phong model per pixel
   - Ambient: constant base color
   - Diffuse: `color × lightColor × max(0, N·L)`
   - Specular: `specularColor × lightColor × max(0, R·V)^shininess`
5. **Shadow Mapping:** Occlude light from shadowed areas
6. **Rasterization:** Convert to screen pixels
7. **Fragment Processing:** Final color computation
8. **Display:** Output to canvas

### Phong Illumination Model

The sphere uses Phong shading, which calculates lighting per pixel:

**Formula:**
```
I = Ka×Ia + Kd×Id×(N·L) + Ks×Is×(R·V)^n
```

Where:
- **Ka×Ia:** Ambient component (base color)
- **Kd×Id×(N·L):** Diffuse component (matte reflection)
  - N: surface normal
  - L: light direction
- **Ks×Is×(R·V)^n:** Specular component (shiny highlights)
  - R: reflection vector
  - V: view direction
  - n: shininess exponent

### Quality Features

1. **Antialiasing:** Smooths jagged edges
2. **High Polygon Count:** 64×64 sphere segments for smooth curves
3. **Soft Shadows:** PCF filtering for realistic shadow edges
4. **Multiple Lights:** Layered lighting for depth
5. **Proper Materials:** Phong model for realistic surfaces

---

## Comparison: WebGL vs Three.js

### Part 1 (WebGL) - Low Level
- **Direct GPU control:** Manual shader writing
- **Buffer management:** Explicit vertex and color buffers
- **Coordinate systems:** Work directly in clip space
- **More code:** Everything done manually
- **Learning value:** Understand graphics pipeline deeply

### Part 2 (Three.js) - High Level
- **Abstraction:** Library handles shader details
- **Convenience:** Built-in geometries and materials
- **Scene graph:** Hierarchical object organization
- **Less code:** Complex features simplified
- **Productivity:** Faster development

### Both Approaches:
- Use GPU for rendering
- Follow same graphics pipeline
- Produce high-quality output
- Require understanding of 3D mathematics

---

## How to Use the Code

### Part 1 (2D Scene):
1. Open the HTML file in a modern web browser
2. Canvas automatically renders the flower pattern
3. Click "Download PNG" button to save image
4. Image saved as "2D_Static_Scene.png"

### Part 2 (3D Scene):
1. Ensure internet connection (loads Three.js from CDN)
2. Open HTML file in browser
3. Scene renders automatically with lighting and shadows
4. Click "Download PNG" to save image
5. Image saved as "3D_Static_Scene.png"

### Browser Requirements:
- WebGL support (Chrome, Firefox, Safari, Edge)
- JavaScript enabled
- Modern GPU recommended for 3D scene

---

## Key Learning Outcomes

1. **Vertex Processing:** Understanding how vertices flow through the pipeline
2. **Color Interpolation:** Automatic blending between vertex colors
3. **Coordinate Systems:** Clip space, world space, view space
4. **Buffer Objects:** Efficient data transfer to GPU
5. **Shader Programming:** GLSL for custom rendering
6. **Lighting Models:** Phong illumination for realistic surfaces
7. **Shadow Mapping:** Real-time shadow generation
8. **Projection Types:** Perspective vs orthographic
9. **Material Properties:** How surface attributes affect appearance
10. **Graphics Pipeline:** Complete flow from vertices to pixels

---

## Technical Notes

### Performance Considerations:
- Static scenes require no optimization
- High resolution increases render time
- Shadow maps use significant GPU memory
- Antialiasing has minor performance cost

### Coordinate Systems:
- **2D Clip Space:** -1 to 1 on both axes, origin at center
- **3D World Space:** Arbitrary units, Y-up convention
- **3D View Space:** Relative to camera position
- **Screen Space:** Pixel coordinates in final image

### File Formats:
- **PNG:** Lossless compression, supports transparency
- **Canvas API:** `toDataURL('image/png')` for export
- **Resolution:** Pixels are device-independent

---

## Conclusion

This assignment demonstrates fundamental computer graphics concepts:
- **Part 1** shows low-level GPU programming with direct buffer and shader control
- **Part 2** applies these concepts using a higher-level library for 3D rendering

Both parts produce high-quality static images that showcase proper geometry definition, color management, lighting, and rendering pipeline usage. The code is well-structured, documented, and meets all assignment requirements including resolution specifications, triangle counts, and visual quality standards.