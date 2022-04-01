# Project 3-2 Write-Up

# **Overview**

This project was mainly a continuation of the previous one, building upon the base that we created, with regards to direct and global illumination. It was set up such that we could do any of two parts out of four available, wherein either of the first two must be chosen as well. The pair that I decided to do was **Parts 1 and 2**. The first part goes more into depth with the bsdf functions set up in 3-1. In particular, it goes into the bsdf for mirror materials, which do reflections, the bsdf for refractive materials, and the bsdf for glass, which is somewhat of a combination of the reflective bsdf and refractive bsdf. The 2nd part also had to do with bsdf's, this time the microfacet bsdf, which is for conductive materials, which include things like metallic surfaces. This one involved the creation of many different terms as separate functions, like F(), G(), D(), etc. and putting them together in the sample_f function, optionally using the f() function, similarly to the previous bsdf problems that we coded before.

## **Part 1**

The following images are at max_ray_depth levels of 0, 1, 2, 3, 4, 5, 100. They are at the lowest settings of 64 samples per pixel and 4 samples per light.

![0](/images/Part1a.PNG) ![1](/images/Part1b.PNG) ![2](/images/Part1c.PNG) ![3](/images/Part1d.PNG) ![4](/images/Part1e.PNG) ![5](/images/Part1f.PNG) ![100](/images/Part1g.PNG)

The reflection in the mirror sphere gets much sharper the more ray depth is increased, although the difference stops being perceptible at around 4 rays deep. The new multibounce effects that would have ideally been present had my glass bsdf worked would have shown newer and finer details in the reflections in the glass, that would have been randomly present with chance R, a term that would have been calculated earlier in the GlassBSDF::sample_f function.

I couldn't get the refraction thing to work, and since I can't debug this project properly, I am unable to comment further on the "bounce numbers" and how they relate to the "particular effects" that appear.

## **Part 2**

The following images of the rendered dragon mesh differ in their roughness values: 0.005, 0.05, 0.25, and 0.5. They are in settings 128 samples per pixel, 1 samples per light, and 5 ray bounces.

![0.005](/images/Part2a.PNG) ![0.05](/images/Part2b.PNG) ![0.25](/images/Part2c.PNG) ![0.5](/images/Part2d.PNG)

As one would expect, the differences manifest in the reflections of light against the surface of the material. At lower alpha values, smoother surfaces, the surface reflects the walls, and the void, with more solid, flatter patches, while the higher alpha values exhibit less noticeable reflections of the colors of those walls/void, retaining more of the original color of the material, which is gold. In each instance the 3-dimensional nature is preserved by the lighting regardless of change.

The following images are rendered with cosine hemisphere sampling, and importance sampling respectively. They are in settings 64 samples per pixel, 1 samples per light, and 5 ray bounces.

![Cos_Hemi](/images/Part2e.PNG) ![Imp](/images/Part2f.PNG)

What's interesting about these two images is that they are exactly the same in quality, with the only difference being light variations in noise. This is expected behavior, as each method of probabilistic sampling is supported by the lighting functions, and the amount of sampling is enough to render a complete image with all of the correct pixels. It's simply a matter of some sampling method being more effective overall than another; importance sampling is just better than cosine hemisphere sampling for the microfacet bsdf.

The next image is of an edit of the CBbunny_microfacet_cu.dae file, where the respective eta and k vector values are changed to be those of the palladium element, instead of copper, at each corresponding wavelength. The roughness was kept the same, for better comparability. The main change was in the color of the material (palladium is grey).

![Pd](/images/Part2g.PNG)

# Miscellaneous

[Link to Proj3-2 Github](https://github.com/cal-cs184-student/p3-2-pathtracer-sp22-calarsentracer2)

[Link to Webpage: https://cal-cs184-student.github.io/sp22-project-webpages-CalArsen/](https://cal-cs184-student.github.io/sp22-project-webpages-CalArsen/)

[Link to Proj3-2 Write-Up](https://cal-cs184-student.github.io/sp22-project-webpages-CalArsen/proj3-2/index.html)
