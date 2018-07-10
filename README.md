# Shader Case Studies
## Wenli Zhao
This readme is a documentation of some shader programming I originally wrote for an [assignment](https://github.com/CIS-566-2018/homework-3-case-study-wpchop) in my procedural graphics class.

The code I wrote for this project resides on shadertoy.com, a really nifty site that lets developers run shader code directly within a webgl-capable browser and view and share their results.

I implemented the [tri-colored cube](https://www.shadertoy.com/view/MdtyRN) and the [bubbling beaker](https://www.shadertoy.com/view/XsccR7) gifs within shadertoy.

### References
#### Tri-colored cube reference:
<img src="https://i.redd.it/e8dcpl3rw32z.gif" width="400px" />

#### Bubbling beaker reference:
<img src="https://cdn.dribbble.com/users/318273/screenshots/2029648/verve_lab_dribbble.gif" width="400px" />

### Tri-colored Cube Implementation Details:
In order to implement the tri-colored cube, I raymarched an SDF cube from an orthographic camera. I positioned the camera at (8,8,8) in world space and adjusted the look, right and up vectors so that it would be angled towards the origin of the cube, (0,0,0). The SDF served as both a clipping mask, as well as an actual cube's position in various stages of the gif.

I broke the gif down into roughly four main steps.
1. Rotating the colors clockwise.
2. Rotating the cube along its y-axis.
3. Rotating the colors counterclockwise.
4. Rotating the cube along its z-axis.

In order to rotate the colors, I used the cube SDF as a clipping mask, and adjusted the colors of the screen as a function of time and an angle, that was calculated based on the screenspace positions of the pixels.
In order to rotate the cube, I applied a transformation matrix to properly rotate the cube. The transformation matrix was computed with an angle that was dependent on time. In order to preserve the colors of the cube, despite the normals shifting, I calculated the components of the normal, and depending on their values in the x, y, or z direction and whether the cube had rotated more or less than 45 degrees, I would color the sides accordingly, based on the gif. 

I found that the most challenging part of recreating this gif was the color preservation on the cube rotations around the y and z axes. It was tricky to map the estimation of the cube's normals to the correct color.

Potential drawbacks:
My shader code isn't well-factored and easy to read. I could probably clean up my code and optimize it a little better in terms of code-readability. As for performance, I have a lot of branching that could probably be cleaned up, but I'm still shaky on how to do this. However, it still runs at 60fps on my machine! :)

### Bubbling Beaker Implementation Details:
For the bubbling beaker, I also implemented an orthographic camera, but instead viewed the scene along the negative z direction, with the camera at (0,0,15). I used the z-direction to maintain the depth of different components of the scene. Each successive component is closer to the camera, and colored differently. These included

1. The outline of the beaker.
    - This was made using an SDF of a triangle prism with a rectangular prism for the neck of the beaker, and two capsules to form the rounded edges of the bottom of the beaker as well as the lip.
2. The dark, teal-colored water.
    - The dark water was constructed using a scaled down version of the triangle of the outer beaker with some additional moving spheres that were smooth-blended with a rectangular prism to create a liquid-like motion. There are also occasional spherical bubbles that attempt to escape the liquid, but get dragged back down!! Muahhaha!!
3. The lighter water with a gradient.
    - The underlying structure was similar to the darker water, but the time was offset so that there would be variation in the waves. Additionally, there is an occasional bubble that rises. I also gave this layer a gradient from a darker blue to a lighter blue. This was achieved by mapping the color values to a subsection of the uv-coordinates in the y-direction.
4. The light bubbles.
    - These bubbles are the top layer. Some bubble all the way up, and some stay inside the beaker. They were a function of time. I used mod to continuously repeat the bubbling up motion. To inject some randomness into the bubbling, I gave each bubble an arbitrary time period during which it would spawn at the bottom of the beaker.
    
Potential drawbacks:
Once again, I think I have a lot of branching that I should clean up. Additionally, I could be smarter about the way I rolled together functions for the bubbles. Also, there is less of a story in my shader than that of the reference gif. My beaker has a more arbitrary and constant bubbling, rather than a gradual rise in bubbling, lots of bursting and then return to a calm state.
