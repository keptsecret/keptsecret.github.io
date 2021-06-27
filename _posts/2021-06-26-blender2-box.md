---
title:      "Blender Artist Notes #2: Unforgotten render"
date:       2021-06-27 16:30:00 +0700
categories: digital-art blender
---
New little render up. I think this one is probably the best one so far, especially in terms of how I wanted it to look vs. how it actually came out.
It works especially well because of the small scope of the environment, which in this case is just part of a workshop tabletop.
If I plan to work on bigger environments, I'll have to brush up on some knowledge before I try it again.
Or just use Unreal Engine (heh).

Had to try a few new things for this scene, so I'll write down some notes here for future reference, as usual.

Final render:

![Final render](/assets/images/20210624-blender2-music-box.png)

_Also, since Blender 2.93 came out a couple of days after I started this, this was done on version 2.92._

## Trying out sculpting and the workflow in Blender

Obviously, the dancer in the box was sculpted.
In hindsight, probably not the best choice since most of the details is blurred and hidden behind the volumetric lighting anyways.
Ultimately, I did learn to properly sculpting and texture the dancer model though, so that's a nice plus.

Although this isn't the first time I've tried sculpting, it's definitely the first time it's been textured and rendered.
This meant I had to learn about the workflow as well, which can be somewhat complicated.

There are two main "tools" for detailed sculpting in Blender: the __Dyntopo__ option and the __Multiresolution__ modifier.
Dyntopo (_dynamic topology_) is an option available in _Sculpting mode_.
From my understanding, it adds polygons (triangles, which is weird) to the model.
That means the level of detail added is relative to how close the viewport camera is to the model, the size of the brush and the likes.

Meanwhile, the Multiresolution modifier (or "multires") is added as a _modifier_ under the "Generate" column.
Apparently, it's an older tool than _Dyntopo_. And it was a bit more confusing to initially use as well.
It works similarly to the _Subdivide_ modifier but geared specifically for sculpting so it has 3 main settings to watch for.
You can subdivide the model to increase the number of subdivisions to be applied to the model.
Then with the 3 settings -- Levels Viewport, Sculpt and Render -- which set the levels of subdivisions to show in the viewport, when sculpting, and for rendering respectively.
You can sculpt the overall shape using lower subdivision levels and then add the finer details with higher levels.
However, _Multires_ doesn't add any polygons, so they will remain as they are (as quads) when you sculpt and only be displaced.
Another difference between _Mutlires_ and _Dyntopo_ seems to be that the former handles fine detailed features better, provided that you give the modifier enough sculpt levels.
[This video](https://youtu.be/Y5Sow63cu80) showcases the difference, which I would guess has to do with the triangles used in _Dyntopo_ and the fact that it also creates polygons relative to the camera.

Now with the sculpted model, you have a ton of polygons, generally too much to render sensibly, so you have to bake the textures and apply them to a lower poly model.
The available ones in Blender are the normal and displacement maps.
Baking with _Dyntopo_ requires two models, the low poly model and the sculpted model, whereas the _Multires_ option only requires one, with the Level Viewport set to a lower level (to act as the low poly model) and the Render set to the (almost) maximum level you went with.

While I think there is a proper workflow to sculpting and baking in Blender (sculpt overall shape with _Dyntopo_ --> retopo the model --> sculpt details with _Multires_ --> bake normals), I went with a simpler method.
I began by modeling a cube in Edit mode into a rough shape of the dancer before adding the _Multires_ modifier to it.
Five subdivisions was enough for this sculpt and I began to shape the model by sculpting at level 2 first, before moving to level 4.
![Comparison before and after sculpts](/assets/images/20210624-blender2-sculpts.png)

A precaution I learned about later on was in using the _Smooth_ brush, where if there aren't enough polygons, smoothing the surface will cause the opposite surface to be pulled as well, causing some vertices to end up in weird positions.
Since my initial model was too rough (at level 0), I switched applied the level 1 mesh to the model instead before I began baking.
UV unwrapping the model was also simpler than I thought and I thought I did a pretty decent job.
Then with the UV unwrapped and render subdivision level at 5, I baked the normals and the displacement textures from the model (a new image texture had to be created first, and I made it 4K 32-bit floats without alphas) and applied it to the level 1 mesh.
![Final UVs: I guess it as good enough](/assets/images/20210624-blender2-dancer-uvs.png)

It worked about as well as I expected it to actually, which was good enough for an object that size in the scene.
After that, I just painted the model and made the material look a bit plastic-y (with subsurface scattering, which might've been too high) and voila!

## Diamond and the science behind it
