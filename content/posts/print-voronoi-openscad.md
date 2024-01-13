+++
draft = false
date = 2019-08-07T22:09:53+01:00
title = "How to 3D Print Voronoi-Style Shapes with Inkscape and OpenSCAD"
slug = ""
tags = ["3dprint", "inkscape", "openscad", "voronoi"]
categories = ["HowTo"]
description = ""
image = "/images/2019-08-print-voronoi-openscad/14_openscad_extruded.png"
type = "post"
+++

I've been into 3D printing for a while now, creating various shapes like mobile phone stands, different types of boxes or just replacements for broken parts. However, one design that I could never quite stop thinking about is the [Voronoi Lamp](https://www.thingiverse.com/thing:584714). Once I saw this, I knew I had to find out how to create such shapes, not necessarily that lamp, but these kind of organic, random connection of edges just got me hooked.

<!--{{< figure src="https://img2.cgtrader.com/items/363151/7f204cd1a4/voronoi-lamp-3d-model-stl.jpg" link="https://www.cgtrader.com/3d-print-models/house/lighting/voronoi-lamp-1900eea1-7396-41f0-a6c0-9e2db8c06245" target="_blank" rel="external" title="Voronoi lamp 3D print model" caption="A 3D print model for a voronoi lamp." attr="Found on [cgtrader.com](https://www.cgtrader.com/3d-print-models/house/lighting/voronoi-lamp-1900eea1-7396-41f0-a6c0-9e2db8c06245), provided by [nik-markellov](https://www.cgtrader.com/nik-markellov).">}}-->
{{< figure src="https://img2.cgtrader.com/items/363151/7f204cd1a4/voronoi-lamp-3d-model-stl.jpg" link="https://www.cgtrader.com/3d-print-models/house/lighting/voronoi-lamp-1900eea1-7396-41f0-a6c0-9e2db8c06245" target="_blank" rel="external" caption="A 3D print model for a voronoi lamp." attr="Found on [cgtrader.com](https://www.cgtrader.com/3d-print-models/house/lighting/voronoi-lamp-1900eea1-7396-41f0-a6c0-9e2db8c06245), provided by [nik-markellov](https://www.cgtrader.com/nik-markellov).">}}

# There is...

tons of different software for 3D modelling out there. The most familiar ones are probably [Blender](https://www.blender.org/) (yes, you can also use it for modelling), [FreeCAD](https://www.freecadweb.org/) or [OpenSCAD](http://www.openscad.org/). I can't really tell why, but I like using OpenSCAD. Albeit its slowness (just a single thread for rendering), its limited syntax, it is still quite powerful and more importantly, it let's me define the shapes programmatically, and I really like that.

# So Voronoi in OpenSCAD?

Unfortunately not, at least not out of the box. [Some people](https://github.com/felipesanches/OpenSCAD_Voronoi_Generator) have written a Voronoi Generator. But in order to use that you have to manually sample the seed points and OpenSCAD has no way of giving you the rendered size of your object. There is [another](https://openhome.cc/eGossip/OpenSCAD/lib-voronoi2d.html) promising solution but it has the same shortcomings as the first.

# Now what? Another tool?

It turns out that I have a history with [Inkscape](https://inkscape.org/). I use it for everything vector-graphic related, like birthday and invitation cards, diagrams, ... So I thought, why not try to predesign my shapes in Inkscape first and then further modify, e.g. extrude, them in OpenSCAD.

You may think, learning a second tool is not the way to go, and you are right. But for me it was not about learning an additional tool, but rather combining the power of the tools I already knew. If you don't feel confident with Inkscape, I suppose there are ways to get your content from other vector-graphic tools to OpenSCAD as well.

In order to get your design exported to OpenSCAD, I use the library from Gael Lafond, which you can find on [thingiverse](https://www.thingiverse.com/thing:2805184/files). Just download and extraxt the files into your Inkscape extension folder. Then, let's go and design our shape.

We start with a simple shape, e.g. a text:

{{< figure src="/images/2019-08-print-voronoi-openscad/1_sesc_eu.png" caption="Simple text, which we later want to print.">}}

The menu entry for the **Voronoi Pattern...** is a little bit hidden. You'll find it here (make sure, your object is selected):

{{< figure src="/images/2019-08-print-voronoi-openscad/2_select_voronoi.png" caption="Where to find the **Voronoi Pattern...** menu entry.">}}

Once you applied the pattern, you should see something like this:

{{< figure src="/images/2019-08-print-voronoi-openscad/2_voronoi_pattern.png" caption="**Voronoi Pattern...** applied to the text.">}}

Hmmm, not quite. It turns out, that the pattern just becomes a fill pattern, thus you still have to enable the stroke.

{{< figure src="/images/2019-08-print-voronoi-openscad/3_add_stroke.png" caption="Patterned text with stroke.">}}

Look's pretty much like expected, right? Unfortunately, you cannot save it to a scad-file yet, because otherwise you'd the the following error:

{{< figure src="/images/2019-08-print-voronoi-openscad/5_error.png" caption="Do not attempt to export the scad-file, yet!">}}

It seems, the extension does not support text. Thus, prior to saving, you need to convert the text to a path, first.

{{< figure src="/images/2019-08-print-voronoi-openscad/6_make_path.png" caption="Convert the text to a path.">}}

Although it looks finished, remember that the voronoi shape still is just a fill pattern, which OpenSCAD cannot handle. The good thing is, that there is an option to convert the pattern, but we need two solid filled copies of our current shape first, because we want a solid shape that covers both, the fill and the stroke of the object.

So, create the copies and apply a solid fill:

{{< figure src="/images/2019-08-print-voronoi-openscad/7_solid_copy-1.png" caption="Add two solid filled copies of the path.">}}

For the first copy, we convert the stroke to a path:

{{< figure src="/images/2019-08-print-voronoi-openscad/8_stroke_to_path.png" caption="Turn the first copy's stroke into a path.">}}

This will result in:

{{< figure src="/images/2019-08-print-voronoi-openscad/8_stroke_to_path_result.png" caption="Result after turning the first copy's stroke into a path.">}}

For the second copy, we convert the fill to a path, then ungroup the elements and union them back together:

{{< figure src="/images/2019-08-print-voronoi-openscad/9_object_to_path_result.png" caption="Result after turning the second copy's fill into a path, ungroup and union.">}}

Now it is time to convert the voronoi pattern from a fill pattern to an object.

{{< figure src="/images/2019-08-print-voronoi-openscad/10_pattern_to_object.png" caption="Turn the pattern into an object.">}}

As you can see, that resulting pattern extends the shape of the original text. You can remove the underlying text with the pattern so that only the pattern as an object remains. In addition the resulting pattern consists just of a stroke, which we convert to an object.

{{< figure src="/images/2019-08-print-voronoi-openscad/10_pattern_to_object-1.png" caption="The resulting pattern object.">}}

This is why you created the solid (second) copy. You now align the solid copy and the pattern, make sure both are selected and ungrouped and create an intersection of both.

{{< figure src="/images/2019-08-print-voronoi-openscad/11_intersect_pattern_with_solid.png" caption="Intersect the pattern with the solid object.">}}

Finally, you need to select the outer stroke and the intersected pattern, align the two and union them.

{{< figure src="/images/2019-08-print-voronoi-openscad/12_final_object.png" caption="The final 2D voronoi object.">}}

Then, save the final drawing as a scad-file, using the OpenSCAD Bezier extension.

{{< figure src="/images/2019-08-print-voronoi-openscad/4_save_copy.png" title="Export to SCAD file." >}}

And that's it, at least for inkscape. You should now have a scad-file with the voronoi pattern.

# 3D Processing in Inkscape
You can directly open this scad-file in Inkscape. You should immediately see a 3D rendering.

{{< figure src="/images/2019-08-print-voronoi-openscad/13_openscad.png" title="View in OpenSCAD." >}}

As it actually has no height, you may want to modify the scad-file by adding a `linear_extrude`. Just look for the line with `Layer_1();` and turn it into `linear_extrude(height=10) Layer_1();`:

{{< figure src="/images/2019-08-print-voronoi-openscad/14_openscad_extruded.png" title="Extrude the object." >}}

# Conclusion
You now should know, how to turn arbitrary shapes in Inkscape into Voronoi patterns, which you may then load into OpenSCAD for potential further 3D processing.

I have to admit, there are quite some steps involved. At least more than I anticipated, which was part of the reason, I wrote this post.

If you have any improvements, just let me know. If you found this post helpful I would be happy to see your designs.
