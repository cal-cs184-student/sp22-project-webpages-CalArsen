# Project 2 Write-Up

# **Overview**

This assignment was all about implementing various functions for meshes, which are ways to represent 3D objects in computer graphics. These were various functions pertained to different aspects of mesh representation, such as the first two tasks, which were about representing the curvature of 3D objects. **Task 1** was about simple Bezier curves, which apply in 2D space, while **Task 2** moved on the Bezier surfaces, which applied Bezier curves multiple times across a 3D plane to get multiple Bezier points, which then applied the de Casteljau algorithm one last time to get a central point to base the 3D surface on. These functions were recursively implemented, such that each call to the evaluate step function would take multiple points as input, and return as output those points processed and weighted by the aforementioned algorithm. **Task 3** was about creating a function that would be used in Phong shading. Since Phong shading requires the normal vectors of every vertex, weighted by the area of the faces around that vertex, I had to implement the normal function for the vertex class, which itself is a HalfedgeElement. I implemented this by iterating over all of the halfedges connected to the vertex, and for each halfedge, navigating its respective elements to find its associated face area, and storing that information for later use at the end to add them all up, and normalize them. The next two **Tasks**, **4 and 5** were about creating operations for the HalfedgeMesh class, to mutate the mesh representation itself in specific ways. The former was about flipping a selected edge to different vertices, and the latter was about splitting a selected edge into two more edges, effectively splitting the faces on either side of the edge. These were implemented through very complicated pointer reassignments, especially the latter task, which involved the creation of new mesh elements. These pointer reassignments were worked out by hand, pictures of which will be shared later on. My implementation of the split function repurposed the first edge element to point to other elements, so no elements are deleted with my code. The last **Task**, **number 6**, was about using these defined flip and split operations to subdivide the entire mesh into smaller faces, the idea being that this is the 3D version of upsampling an object, analogous to the upsampling operations from assignment 1. This took the longest to implement, as many steps were required. First the new positions of old and new vertices were computed and stored in vertices and edges respectively, then every edge of part of the original mesh was split, then every split edge that connected old and new vertices was flipped, then the previously stored new positions were applied to every vertex in the new mesh, thereby completing the 4-1 subdivision.

## **Task 1**

The way Bezier curves are implemented in evaluateStep is with the de Casteljau algorithm, an algorithm that takes every adjacent pair of vertices in a list of vertices, linearly interpolates new points between those pairs as output, and through some function outside evaluateStep, recursively calls evaluateStep until there is only one point left, that creates the Bezier curve that connects the first and last vertices of the original input list of vertices. I implemented that evaluateStep function exactly as I described the algorithm, with a vector of Vector3D's to hold all the interpolated points, and a special case to catch when a single point is the input to the function, to return that single point.

![Task1a](./proj2/Task1a.PNG) ![Task1b](./proj2/Task1b.PNG) ![Task1c](./proj2/Task1c.PNG) ![Task1d](./proj2/Task1d.PNG) ![Task1e](./proj2/Task1e.PNG) ![Task1f](./proj2/Task1f.PNG)

Here I created my own Bezier curve with 6 points, and took pictures of each step down to the final curve. The last picture is one where I moved two of the points, and scrolled the parameter t to a value presumably closer to 1.

## **Task 2**

The de Casteljau algorithm extends to Bezier surfaces as follows. Since surfaces are 3-Dimensional in nature, the input to the evaluateStep function is now stored as a vector of multiple vectors of Vector3D's, that is to say, the controlPoints variable holding the original control points of the Bezier Patch contains multiple lists of points, where each list must be evaluated with the algorithm to reach individual points. Those individual points are collected from each list to form a new final list of points that then needs to be interpolated again with the very same algorithm. These are performed with different parameters u and v respectively. This task is implemented as a function "evaluate", that performs that interpolation for each list in the controlPoints, stores the values in a new list, thereafter performing one last iteration of the algorithm to output one single point. To this end, they provided an evaluate1D function, whose purpose was to perform evaluateStep multiple times until the final point is reached. This simplified the code a little. Otherwise, evaluateStep was implemented entirely the same as in the first task.

![teapot.bez](./proj2/Task2.PNG)

The picture above is of the implementation at work, on the teapot.bez file.

## **Task 3**

Earlier I described how I helped implement Phong shading by designing a function that computed area-weighted normal vectors for each vertex in a mesh. This function was implemented by taking whatever halfedge the vertex points to, and iterating using that halfedge to get all the adjacent vertices' unique positions. Each time this was done, the vector cross product of two adjacent vertices was taken, normalized, multiplied by its magnitude, and added to a list called area_weighted_normals. This list was summed to obtain a final vector that was then normalized and returned by the function. To reduce the clunkiness of the for loop I initially had, I went online to look for better ways of doing for loops, and found a format that I liked on stack overflow. This was for the last loop.

![teapot.dae w/o normals](./proj2/Task3a.PNG) ![teapot.dae w/ normals](./proj2/Task3b.PNG)

The first picture is of the teapot.dae file rendered without vertex normals, while the next one is of the same file rendered with the vertex normals in place. Notice that with the normals implemented, the shading is a lot smoother and less blocky than when the teapot had no area weighted normal calculations.

## **Task 4**

As I mentioned earlier, this task and the next one was very heavy with pointer reassignments. As this is a function for the HalfedgeMesh class, I had access to every one of the elements in the overall mesh, and since the function takes some EdgeIter as input, I could use the data structure of that element to access every adjacent element to do the reassigning. Much of the work put into this function was in reading and understanding the way the HaldedgeMesh structure worked, and how its constituents worked, to be able to manipulate those pointer assignments in the function to come. There was also a lot of writing I had to work out to make sure I didn't miss any pointer assignments, because missing some assignments could have led to some of the elements accidently getting deleted. I wrote some of this on one of my in-n-out receipts:

![in-n-out receipt](./proj2/Receipt1.jpg)

It should be stated henceforth that I did not integrate any support for boundary cases for the rest of the project (there was no possible way for this task to have boundary support, as an aside). For this function as well, no new elements were created, so that simplified some of the "working out the problem" process.

Here is the teapot.dae file before and after some flips, to showcase the function:

![teapot.dae pre-flip](./proj2/Task4a.PNG) ![teapot.dae pro-flip](./proj2/Task4b.PNG)

## **Task 5**

This task was somewhat similar to the previous task, except a decent amount harder. The main difficulty arose in having to keep track of more elements in the mesh, as some new ones inevitably had to get created. To keep track of this, I used the back of the in-n-out receipt I used for the previous task:

![in-n-out receipt back](./proj2/Receipt2.jpg)

As one can see, there was more that needed to be kept track of, so I had to organize the information a little bit better, and draw a better diagram to keep track of everything. For both, this task and task 4, I organized every adjacent element by giving everything a name that denoted whether one element was vertex a, b, c, etc. and whether and edge or face or something else could be identified by its surrounding elements, like mde is the edge for m and d, or mabf is the face for vertices m, a, and b. Since the orientation is always consistent, given the implementation of the data structure, I could always rely on these vertices always being in the same place every time the operation needed to be carried out on some input edge. This organization can backfire a little, as at one point I misspelled a variable name that led to a glitch where one of the faces would sometimes not point to a halfedge anymore, resulting in a mesh that would have a face disappear sometimes when carrying out the split function. Besides the pointer reassigning, the position of the new vertex is calculated with math that takes the midpoint between the split edge's two vertices.

Here are some screenshots of the bean.dae mesh before and after some edge split operations:

![bean.dae pre-split](./proj2/Task5a.PNG) ![bean.dae pro-split](./proj2/Task5b.PNG)

I decided for the both split and flip mesh that I would reuse the after-flips mesh of the teapot.dae mesh I showed for task 4, with some edge splits added.

![teapot.dae](./proj2/Task4a.PNG) ![teapot.dae pro-flip](./proj2/Task4b.PNG) ![teapot.dae flips and splits](./proj2/Task5c.PNG)

## **Task 6**

I implemented the loop subdivision closely following what the spec recommended in the description for task 6. The first part, all of the new positions for the old vertices, is computed upon each iteration of a for loop that goes over every single vertex in the old mesh, and stored in each VertexIter's newPosition variable, to be assigned to its actual position later. Values like n and u are computed each iteration as well, and the loop contains a nested while loop to gather adjacent vertex position values, necessary for overall computation. The second part is where the newPosition values for new vertices are computed. These are made by iterating over every edge that will be subsequently split (ignoring boundaries), and storing the computation in each EdgeIter's newPosition value, which will be assigned to the new vertices' newPosition variables when they get created. Part 3 is when all the splitting occurs. This iterates over every single edge once again, this time splitting each edge if it is not a boundary, and not a new edge in the mesh. A distinction is made between new blue edges and new black edges, as technically the edge below the split edge is not supposed to be flipped later on. This color scheming follows this diagram used in the spec:

![blue black diagram](./proj2/Task6a.PNG)

Part 3 is also where those aforementioned EdgeIter newPosition values are given to the newly created vertex newPosition variables, and those vertices' isNew values are set to true. At one point during development, I tried to combine parts 2 and 3, but this led to uneven mesh subdivision, as the calculation made in part 2 would take the vertices of already split edges instead of using ones from the original mesh.

Moving on to part 4, I put in another for loop, again, this time to iterate over every single EdgeIter, and flip any edges that were new, but not black, and were connected to a new vertex and an old vertex, using isNew values assigned earlier. This is also where the isNew and isBlackandNew booleans get set to false again, so that subdivision can be called again on these edges in the future. The last part is once again a for loop, this time for every single vertex. This iterative loop will go over new vertices as well, since every call to splitEdge created the new vertices and stored their data. This loop simply ignores boundary vertices, sets new vertices' isNew values to false for future subdivision, and assigns the newPosition values of every vertex to its individual position value. I should mention that at one point, one of the materials I read to get ready for this task mentioned that using for loops could result in some errors with these iterators, as sometimes elements get deleted, but since none of my functions deleted any elements of the mesh, I felt that I could use for loops safely enough.

Here is the ./dae/torus/input.dae mesh before subdivision, and after two loop subdivisions:

![pre-loop](./proj2/Task6b.PNG) ![post-loop](./proj2/Task6c.PNG)

One thing to notice between the two pictures is that all of the sharp edges present in the first picture are gone, in favor of more smooth curvature around the original six points around the mesh. Although there is still some sharpness between the mesh triangles, the angles connecting them are much more widespread, counter to the more acute angles in the original mesh that gave a sharper feeling.

Here I tried to keep some of those sharper edges by splitting the mesh up a little before performing the two loop subdivisions:

![pre-loop2](./proj2/Task6d.PNG) ![post-loop2](./proj2/Task6e.PNG)

As a means to reduce the loss of sharp edges that were originally present in the mesh, I tried to pre-split two of those edges at one of the corners of the "torus". The idea was to make those edges more complicated, that way when the subdivision occurred, some of the smaller triangles would remain in place, and would not subdivide as dramatically as the rest. I did this with the thought in mind to remove some of the longer, lonelier edges, and split them into multiple small, web-like edges.

This is the cube.dae after perform a few loop subdivisions:

![cube](./proj2/Task6f.PNG) ![cube](./proj2/Task6g.PNG)

Although it starts out with some symmetry all around, it eventually loses that and becomes somewhat asymmetrical in shape. It's shape morphs outwards a little at some corners, while some corners morph a little inwards. This is because the original edges didn't have symmetry across the entire cube. When I split every edge on the cube so that all sides were symmetrical with each other, the cube remained symmetrical after all of the loop subdivisions:

![cube](./proj2/Task6h.PNG) ![cube](./proj2/Task6i.PNG)

# Miscellaneous

[Link to Proj2 Github](https://github.com/cal-cs184-student/p2-meshedit-sp22-calarsen)

[Link to Webpage: https://cal-cs184-student.github.io/sp22-project-webpages-CalArsen/](https://cal-cs184-student.github.io/sp22-project-webpages-CalArsen/)

[Link to Proj2 Write-Up](https://cal-cs184-student.github.io/sp22-project-webpages-CalArsen/proj2/index.html)

**Side Note** - The proj2 file itself contains a file named curve_for_task1.bzc, which I used for one of the pictures in the task 1 deliverable. The other meshes were ones already provided by the initial commit of the project.
