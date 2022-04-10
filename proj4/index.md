# Project 4 Write-Up

# **Overview**

The cloth simulator project was a series of coding segments that, when all combined, would create a grid of point masses connected with springs that would draw in a cloth graphically based on the positions of those springs and point masses, and simulate the external and internal forces that act upon a piece of cloth over a period of given time. In particular the **first** coding segment involved the construction of the grid of point masses; this was a function that looped over every single point that would go into the cloth grid, provided by the cloth structure's width, height, num_width_points, etc, and created a new PointMass object to hold that position information, as well as multiple Spring objects that that held information about PointMass' positions depending on which of the three spring types the object was. Every object was stored in the cloth struct's respective vectors as cloth components. The **second** part was to simulate every force that acts upon the cloth's system of point masses and springs. External forces such as gravity are computed with simple F = ma equations (every point mass weighs an equal amount distributed over the whole grid). Spring corrective forces were computed via a special version of Hooke's Law: F = ks * (||pa - pb|| - l), where the parenthesis indicate the displacement of each end of the spring from its rest length positions. Using every built up force, a for loop calculates the next likely position of each PointMass with a simplified Verlet Integration equation. Lastly, a famous correction from Provot is applied, wherein each spring is constrained to only deform by a maximum of 10% more than the spring's rest length. **Part 3** further expands on the position updating of point masses by including collision support with other primitives in the scene. Support is added for spheres and planes, where both were coded to detect when a point's new position would cross the bounds of a primitive object, and would change its position to be the last position plus some corrective vector that would push that last position to some calculated "tangent point" not located across the barrier of the primitive. This involved a reverse LERP that I worked out on paper for the sphere, and a projection problem for the plane. As for the **fourth** section of the project, this was self-collision support. To achieve support for this, I had to implement a spatial map supported by a bucket hash system. Basically, the entire scene (3D space) is split into theoretical boxes of arbitrary size, and each point mass's position is recorded as being in one of these boxes. After the unordered_map is filled with corresponding vectors of PointMass pointers that indicate which point masses belong to which box in the 3D space, the simulation can "simulate" self-collisions that each point mass of the cloth struct has, using the box system to reduce the amount of times calculations with other candidate point masses have to be made. Each collision corrects the original position with a correction vector that is the average of multiple correction vectors with each candidate PointMass that violates some distance threshold with whatever PointMass is being tested for self-collision. The **final** part of the project was implementing different shaders for the scene. These were all done in GLSL language, with a system of .vert and .frag files that overlook part of the rasterization pipeline to ensure that samples that are drawn to the screen follow whatever guideline of shading is implemented in the aforementioned files. The shaders were diffuse shading, Blinn-Phong shading, texture mapping, displacement and bump mapping, and environment-mapped reflections.

## **Part 1**

Here are some screenshots of scene/pinned2.json, to show off the wireframe:

![wireframe](/images/Part1a.PNG)

The following are no shearing constraint, only shearing constraints, and all constraints:

![wireframe](/images/Part1b.PNG) ![wireframe](/images/Part1c.PNG) ![wireframe](/images/Part1a.PNG)

## **Part 2**

Messing around with the ks of the cloth in the simulation seemed to make the cloth much more elastic with lower ks (100), and much more solid with higher ks (100000). That is to say lower ks had the cloth moving much more loosely and chaotically, while higher ks made it more of a denim cloth. The pictures can be seen below:

![wireframe](/images/Part2a.PNG) ![wireframe](/images/Part2b.PNG)

Weirdly enough lowering the density to about 0.4 made the cloth behave very similarly to when it had just a high ks value, where the cloth wasn't prone to distortion as much. While increasing the density to 60 made the cloth behave as a mix of low ks cloth, and regular cloth, where the cloth does have some flexibility, but is mainly focused on falling down due to gravity's effects.

![wireframe](/images/Part2c.PNG) ![wireframe](/images/Part2d.PNG)

Making the damping coefficient at something like 0% made the cloth very sporadic in motion as it fell down from rest, while raising it to about 85% made the cloth fall down very slowly, as though it were dipped in some molasses. It also didn't distort much under high damping.

![wireframe](/images/Part2e.PNG) ![wireframe](/images/Part2f.PNG)

Here is a screenshot of scene/pinned4.json working right after my Part 2 implementation:

![wireframe](/images/Part2g.PNG)

## **Part 3**

Here are the sphere scenes at ks = 5000, ks = 500, and ks = 50000:

![wireframe](/images/Part3a.PNG) ![wireframe](/images/Part3b.PNG) ![wireframe](/images/Part3c.PNG)

The differences very much so reflect the observations that were seen in part 2. At lower ks, the cloth is much more open to displacement, and much less rigid about spring constraint, and so it wraps itself around the sphere more loosely, hanging down pretty far as gravity is the force affecting the cloth the most. Meanwhile, the high ks means that the springs have more influence over the forces acting on the cloth, so while the cloth still warps around the sphere, it does so less tightly than before, as the cloth also exhibits some unwillingness to hang completely straight down due to the rest of the cloth holding up some parts of itself. The default ks value makes the cloth drape over the sphere "normally" so to speak.

As an aside, this part of the project gave me some trouble in figuring out the corrective vector for both, sphere collisions and plane collisions. The sphere correction with R kept giving me values that blasted the cloth right off the sphere in a glitchy mess, and it was only until I came up with a reverse LERP that used the tangent point as the goal variable that I was able to get the simulation to be stable. The plane just took a while to get right because I had to look into projection formulas online.

Here is that plane scene working perfectly fine:

![wireframe](/images/Part3d.PNG)

## **Part 4**

This is what the self-collision scene looks like with default parameters (some clipping was present that is not visible in these images):

![wireframe](/images/Part4a.PNG) ![wireframe](/images/Part4b.PNG) ![wireframe](/images/Part4c.PNG)

The scene with a higher density value seemed to break the code a bit, as more clipping in the cloth occurred than was previously seen. The "folds" of the cloth were also much smaller than before as the cloth also bundled in the middle much more "densely" as it were. In a sense it was somewhat like the scene before self-collision support was added, although not quite as clippy as that was.

![wireframe](/images/Part4d.PNG) ![wireframe](/images/Part4e.PNG) ![wireframe](/images/Part4f.PNG)

When I adjusted the ks value instead to be much higher, it made the cloth very resistant to changes in movement, and gave the illusion of something like a heavy, rubber tarp, almost simulating a denser material better than adjusting the density did. There was almost no clipping to be seen with this setting. The simulation itself also ran a bit slower as well, similar to when the damping was increased in the showcase of the Part 2 deliverable.

![wireframe](/images/Part4g.PNG) ![wireframe](/images/Part4h.PNG) ![wireframe](/images/Part4i.PNG)

As an aside about hardships with coding, I actually had an issue with getting the Horizontal positioning of the cloth to work. It was mainly the rand() function, and getting the random numbers between -(1/1000) and 1/1000 so that the cloth could actually fall correctly. It turned out that, in my LERP implementation of getting this random variable, I was accidently getting 0 values by multiplying and dividing by integers in my calculations, so it was just something small like that. Otherwise the hash position gave me some trouble too; I just googled around and found something called a "Cantor Pairing Function" to uniquely get the id's of boxes for the spatial map. Oh, and I also had an issue with the calculation of the average corrective vectors, specifically for PointMasses that didn't collide with anything. It was a simple divide by zero error that cleared the position of each PointMass if there was nothing to "average" over.

## **Part 5**

A shader program, as I saw it, was a program that speeds up the process of shading something in a given scene by using vertex and fragment shader files to process each sample in a scene swiftly, and correctly for whatever the scene parameters are. This is implemented so as to alleviate some of the strain on the computer, so this sort of process happens alongside computation of the cloth positions in a streamlined fashion. In that sense, .vert and .frag files work together to streamline the rasterization process of drawing samples onto a screen, such that the points in the scene are correctly shaded, or lit up for the viewer.

The Blinn-Phong shading model works by incorporating ambient lighting, diffuse reflection, and specular reflection all into one equation that outputs the amount of luminous intent that a point carries given at least a single light source. This uses the same normals, and spherical cartesian coordinates as was seen in Projects 3-1 and 3-2. Here are the screenshots of only ambient lighting, only diffuse reflection, only specular reflection, and finally everything together:

![wireframe](/images/Part5a.PNG) ![wireframe](/images/Part5b.PNG) ![wireframe](/images/Part5c.PNG) ![wireframe](/images/Part5d.PNG)

This is the texture mapping shader results, which uses a built-in texture() method from GLSL, and a picture I used previosly on Project 1 of some wetlands:

![wireframe](/images/Part5e.PNG)

The bump mapping sphere and cloth:

![wireframe](/images/Part5f.PNG) ![wireframe](/images/Part5g.PNG)

The displacement mapping sphere and cloth:

![wireframe](/images/Part5h.PNG) ![wireframe](/images/Part5i.PNG)

The two approaches were very similar in that they both computed new normals for each vertex so as to map a height map's texture to a surface in a way that emulates an uneven surface on something that is otherwise flat. I actually ended up with the exact same .frag code for each shader, code that calculates the new normal via the height uv map and the TBN matrix (the only difference being the height map sampling, which I multiply by 55 in my bump map shader to get the texture to appear more prominently, it doesn't work so well in displacement mapping). While the bump.vert file is mostly the same as default.vert, the displacement.vert file varies in its calculation of a new theoretical position that would match whatever normal the height map sample corresponds to.

For one reason or another, the way the two shaders react to the sphere, especially with changing the sphere mesh's coarseness, does not actually change much. The only real perceptible difference with displacement mapping was that the ball was slightly smoother, with less wrinkles from changed positions. While the bump mapping only showed a difference in the moire patterning and straightness of lines between the increased coarseness of the sphere (a difference also perceptible in the displacement mapping tests).

Tests for Displacement Mapping, -o 16 -a 16 versus -o 128 -a 128:

![wireframe](/images/Part5j.PNG) ![wireframe](/images/Part5l.PNG)

Tests for Bump Mapping, -o 16 -a 16 versus -o 128 -a 128:

![wireframe](/images/Part5k.PNG) ![wireframe](/images/Part5m.PNG)

These final pictures are of the mirror shader and the default environment map:

![wireframe](/images/Part5n.PNG) ![wireframe](/images/Part5o.PNG)

# Miscellaneous

[Link to Proj4 Github](https://github.com/cal-cs184-student/p4-clothsim-sp22-clotharsen)

[Link to Webpage: https://cal-cs184-student.github.io/sp22-project-webpages-CalArsen/](https://cal-cs184-student.github.io/sp22-project-webpages-CalArsen/)

[Link to Proj4 Write-Up](https://cal-cs184-student.github.io/sp22-project-webpages-CalArsen/proj4/index.html)
