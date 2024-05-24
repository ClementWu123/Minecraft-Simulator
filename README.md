# Mini-Minecraft
Team Member: Ruijun Zhong, Yuetian Zhao, Keqi Wu

# Milestone1
<details>
  <summary> Specifications </summary>
  
## Game Engine Tick Function and Player Physics (Ruijun Zhong)
**Player Movement Control**
  * **F key**: Enable or disable flight mode
  * **Mouse Movement**: Control Camera Orientation using polar coordinates.
  * **WASD Keys**: directional player movement relative to the camera's orientation, enhancing navigation within the voxel-based environment.
  * **Space Key**: Activates the jump functionality, allowing the player to overcome obstacles and explore vertical terrain features.
  * **Q&E Keys**: Accelerate postively or negatively along up vector during flight mode
  * **Shift  Key**: Engages a speed multiplier, providing the capability for accelerated movement across expansive terrains.  

**Physics**
  * **Gravity Simulation**: Applies a constant downward force on the player, necessitating strategic navigation and engagement with various terrain elevations.
  * **Friction Implementation**: Decelerates player movement over time, preventing unrealistic perpetual motion and enhancing the realism of player-terrain interactions.
  * **External Forces**:  Considers additional environmental forces, enabling dynamic player responses to various game world stimuli.

**Collision Detection**
  * **Grid Marching Technique**: Utilizes ray casting for precise collision prediction and prevention, ensuring terrain solidity is respected.
  * **Terrain Interaction:**: Modifies player movement based on detected voxel collisions, maintaining consistent and realistic interactions within the game environment.
  * **Ray Casting VS Object-Object Intersection Preference**: Selected over AABB intersection to improve collision detection reliability, crucial for avoiding missed collisions at lower frame rates and preventing "tunneling" through terrain.

  **Block Interaction**
  * **Left Mouse Click**: Removes a block from the terrain. This action utilizes a precise ray casting technique to determine the specific block the player is targeting, allowing for accurate and intuitive terrain modification.
  * **Right Mouse Click**: Places a new block adjacent to the targeted terrain block. The placement algorithm ensures the new block is positioned in direct relation to the block face the player is looking at, providing a seamless and intuitive building experience.
    
## Procedural Terrain (Yuetian Zhao)
**Grassland** : The grasslands terrain is generated using two-dimensional fractal Brownian Motion (fBM) driven by a Perlin noise function.

**mountains** : The mountains terrain is friven by fractal perlin noise, in order to make mountains sharper we use pow function and smoothstep for transition. 

**ProcTerrainGen** : The class stores all the noise function used in milestone1 and for future use.

## Efficient Terrain Rendering and Chunking (Keqi Wu)
**Chunk Inherited from drawable**

The primary goal is to accumulate VBO (Vertex Buffer Object) data for a chunk and store this data in memory to facilitate rendering. In the process of gathering VBO data, only the faces of opaque blocks that are adjacent to empty spaces (air) are considered for rendering. This entails appending the vertices, normals, colors, and UV coordinates of these faces to the VBO data. For blocks situated at the boundaries of a chunk, adjacent chunks are consulted to determine the status of blocks neighboring those at the edge.

**Interleaved VBO Data**

Given that the setup includes just a single buffer array besides the index buffer array, it's necessary only to use generateBuffer() along with POSITION, NORMAL, COLOR. Following this, the buffer data should be linked with POSITION, NORMAL, COLOR. Within shaderprogram.cpp, the addition of a drawInterleaved(Drawable &d) function facilitates the drawing of the buffer. This function details the starting points for each type of data—position, normal, color, UV coordinates—and outlines the stride required for accurately accessing each piece of information.

**Terrain expansion**

During each update cycle, the program verifies if the 81 chunks around the player, forming a 9 x 9 chunk area, have been initialized and whether their VBO data has been generated. If any chunks have not been created or their VBO data is missing, the program proceeds to instantiate these chunks and generate the necessary VBO data. There's a specific member variable, named m_ChunkVBOs, responsible for holding the VBOs of all chunks that are currently loaded. Within the draw function, there's a loop that traverses this chunk, rendering each chunk for which VBO data exists.
</details>

# Milestone2
<details>
  <summary> Specifications </summary>

## Cave Systems & Multithread(currently got issue here) (Keqi Wu)
* **Caves Generation**: 3d Perlin Noise was used to generate the cave systems, which are uniformly distributed beneath the entire surface terrain. If the noise value returned by getCaveHeight(x,y,z) is less than zero, we place STONE blocks; otherwise, we place LAVA or EMPTY blocks based on height.
*  **Collision Detection**: To prevent collisions with transparent objects, we do not set the velocity to zero if the hit block is transparent (WATER, LAVA).
*  **Post Processing**: Added color offset when under water and lava.
*  **multithread**: Also tried to implement another version of Multithread, containing some thread designing issues that cause crashes randomly. The terrain can be generated on my machine but not on team members machine (on MS1). When migrate to MS2, the program crashes.

## Texturing and Texture Animation (Ruijun Zhong)
**Player Movement Control**
  * **Sample From Texture Atlas**: Sample the texture from a texture atlas to consolidate multiple textures into a single program, reducing drawcalls and optimizing performance in real-time rendering.
  * **Texture Animation**: Animate the texture to enhance realism
  * **Transparent Blend Rendering**: Employ transparent blending techniques by rendering opaque objects first and transparent objects second, using separate Vertex Buffer Objects (VBOs) to avoid issues caused by the depth buffer. This approach ensures that if the transparent rendering is incorrect, it won't obstruct the visibility of other objects.

## Multithread & lava/water swim (Yuetian Zhao) 
**multithread**
  * **BlockTypeWorker**: use noise to generate terrain information
  * **VBOWorker**: used to generate data for our VBO and then pass to the shader.
  * **Swim**: swim in lava and water : change the velocity, so player can swim in the lava and water
</details>

# Milestone3
<details>
  <summary> Specifications </summary>

## Normal Mapping & Shdaow Map & Water Wave (Lighting) & Distance Fog & Defered Rendering Pipline(Ruijun Zhong)

* **Normal Map**: Apply Normal to make Minecraft Cube realistic

* **Shdaow Map**: For the Shadow Map implementation, Percentage-Closer Filtering (PCF) is utilized to enhance the visual quality of shadows. This technique softens the edges of shadows, making them appear more natural and less pixelated. The depth of objects from the light's perspective is recorded using a light view depth map, which is crucial for determining whether a pixel is in shadow or lighted. This method helps to accurately simulate the effect of shadows cast by light sources in 3D environments.
  
* **Water Wave (Lighting)**: The water wave simulation incorporates advanced lighting models to achieve realistic effects. Using the Blinn-Phong lighting model, specular highlights are rendered on the water surface based on the viewer and light direction, enhancing the visual perception of water surface undulations. Additionally, the Fresnel effect is employed to adjust the reflectivity of the water surface depending on the viewing angle; the water becomes more transparent as the viewing angle approaches grazing angles. This dynamic interaction of light with the water surface brings a lifelike quality to the scene.

* **Distance Fog**: The implementation of Distance Fog involves a technique that simulates atmospheric effects by gradually increasing the opacity of fog with distance from the camera. The color and density of the fog are dynamically adjusted based on the depth of the scene and a procedural skybox color, which allows for a seamless blend of the fog with the background sky, creating a depth cue and enhancing the perception of distance in the virtual environment.

* **Deferred Rendering Pipline**: The Deferred Rendering Pipeline is a powerful rendering technique used to handle multiple light sources efficiently in complex scenes. In this method, the rendering process is split into two main phases: the geometry pass and the lighting pass. During the geometry pass, data about scene geometry, such as positions, normals, and material properties, are captured in textures (G-buffers) without any lighting calculations. In the subsequent lighting pass, these textures are used to perform lighting calculations for each pixel independently, which allows for handling numerous dynamic lights and complex material interactions more effectively. This technique is especially beneficial for scenes with high geometric complexity and diverse lighting conditions.

## Day and night cycle & Post-process Camera Overlay & Water Wave (motion part) (Keqi Wu)

* **Day and night cycle**: Sky GLSL fragment shader crafts a dynamic sky environment by altering light and color based on the sun's continually changing position. Utilizing an inverse view projection matrix, it transforms screen coordinates to world coordinates, establishing the foundation for simulating atmospheric effects. The shader calculates ray directions from the camera, essential for rendering the light scattering across the sky. Worley noise generates animated, realistic cloud textures, contributing depth and movement to the sky. Sphere-to-UV mapping is employed to apply these textures onto a simulated spherical dome, enhancing the visual impression of a curved atmosphere. Color transitions are meticulously handled, shifting between distinct palettes for noon, sunset, and dusk based on the sun's elevation and angle relative to the observer. This blending is tuned to reflect the sun's position, with special effects like a glowing sun that dynamically changes in appearance and intensity. The result is a visually compelling sky simulation that enhances the realism and depth of 3D scenes, making the shader integral to immersive outdoor environments.
  
* **Water Wave (motion part)**: The vertex shader simulates water wave motion on geometry marked as "animated" by utilizing vertex attributes. This effect is achieved by applying a sine and cosine function to the world-space coordinates (x, z) of each vertex. The amplitude and frequency of the waves are varied by using a noise function based on the vertex position, creating a more natural and less uniform appearance. The calculated wave offsets the y-coordinate of the vertex position, giving the impression of undulating water. The shader ensures the adjustments are perspective-correct by scaling the offset by the w-component of the clip space position, enhancing the realism of the effect.

* **Post-process Camera Overlay**: The water GLSL fragment shader simulates a dynamic water effect by blending textural data with procedural noise. It retrieves color from an albedo texture and modifies it using a fractal brownian motion (fbm) function, which applies cubic interpolation for noise generation across three-dimensional space. This noise influences the texture's brightness, simulating light interaction with moving water surfaces. Additionally, the shader creates a shimmering effect using a complex trigonometric transformation, enhancing the water's visual complexity. This combination of texture manipulation and procedural generation creates a realistic and dynamic water overlay. The lava shader simulates fluid flow across surfaces by computing a noise-based distortion field. It uses a noise function to determine the flow direction and intensity at various points, adjusted dynamically by the shader's time variable, simulating natural fluid movement. This is further processed to compute gradients and influence the flow's directionality. The result is visually represented as a color modulation over the albedo texture, adding a sense of depth and motion to the rendered surface, mimicking the appearance of flowing, viscous material.

## Additional Biomes & Procedurally placed assets (Yuetian Zhao)
* **Additional Biomes**: Incorporate the new block types 'SNOW' and 'SAND' into terrain generation system. Utilize two distinct noise functions, Perlin noise and fbm, to generate detailed maps representing moisture and temperature distributions across your virtual world. These maps serve as dynamic templates, capturing the nuanced variations in moisture levels and thermal gradients that influence biome formations. Leveraging the information from these maps, implement an intelligent biome determination mechanism. This algorithmic approach analyzes the moisture and temperature data at each point of your terrain, allowing it to categorize regions into different biomes such as lush grasslands. By defining specific thresholds and criteria based on environmental factors, system can accurately discern which biome characteristics are prevalent in each area. Once the biomes are identified, integrate them into terrain generation pipeline. As the terrain takes shape. For instance, 'SNOW' blocks should adorn frigid landscapes characterized by low temperatures and high moisture, while 'SAND' blocks find their home in arid expanses with minimal moisture content. Simulating natural ecosystems where different biomes coexist harmoniously.

* **Procedurally placed assets **: Add CACTUS, RED_FLOWER, AND LONG/MID GRASS to the biome, for trees and cactus, use two noise function to determine one region, and in that region, only one tree can exist, for grass and flower, use one perlin noise to determine a position should place asset or not.   
</details>
