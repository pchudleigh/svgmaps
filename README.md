Map Level SVG File Specification
================================

Compositing
-----------

Each shopping centre's map requires a series of SVG files creating, one for each level of the map. The original source SVGs for these maps consist of multiple files per level which must be combined, scaled and correctly aligned, ideally using a graphics tool such as Adobe Illustrator. The source artwork typically consists of one file containing walls, walkways and the overall outline, another consisting of the ceilings for each unit, and another for the fill. The retail unit ceilings are the distinct shapes representing retail units for the purposes of areas that can be interacted with, highlighted and have retailer logos attached to. As such it is vitally important that these are correctly tweaked to ensure there are no gaps in the outline.

All other elements within the SVG files (e.g. text labels, symbols) should be removed.

SVG Specification
-----------------

In order to work properly with the maps system, the individual SVG files for the levels of a given shopping centre have to be constructed according to a specific format. A skeleton SVG file is shown below.

    <?xml version="1.0" encoding="utf-8"?>
    <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
    <svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" xml:space="preserve">
    <g class="level" id="level0">
      <g class="shadow">
        <path d="…" />
      </g>
      <g class="outline">
        <path d="…" />
      </g>
      <g class="background">
        <path d="…" class="walkway"/>
        <path d="…" class="wall"/>
        …
      </g>
      <g class="ceilings">
        <path class="unit" d="…" id="1001" />
        …
      </g>
    </g>
    </svg>
    
The SVG file should then be located within the repository at `/zend/public/maps/<centre_identifier>/levels/level<number>.svg`. Most graphical SVG tools will add additional attributes or elements to the saved document; these should all be removed with the exception of the classes and IDs discussed below, both to reduce the document weight and ensure no conflicting or erroneous styling information is preserved. All colours should be removed, for example; these are defined externally through CSS.

Key Elements
------------

Every level SVG file should have a root level group element with a class of `level`, and an id of `level<x>` where x is an integer value representing its order in the shopping centre, starting with zero. Within this there are four other key groups, in this order:
  
* shadow — this contains the path for the shadow of the entire level.
* outline — this contains the path for the outline stroke for the level — in the current map this appears as an orange border on the level itself.
* background — this contains paths representing two types of item; the walkways and the walls of the individual retail units. These should be given a class of `walkway` and `wall` respectively. Typically there will only be a single path for the walkway, but multiple paths for walls. The walkway should be first so as to ensure it is stacked underneath the walls when the maps is drawn.
* ceilings — this contains the paths representing the individual retail units (specifically, the ceilings, since that's the part that's distinguishable). Each path should be given a class of unit, and an id representing a unique identifier for that retail unit. The value of this identifier is then used by the retailer JSON feed to associate a retailer with a given retail unit. The convention here is that units should be given an identifier of four digits, prefixed with a value representing the floor, e.g. 1001 would be on the first level (level 0).

Whilst this example uses path SVG elements, it's equally possible for a map to have components comprised of other SVG primitives, such as `polygon`, `circle` or `ellipse`. In these instances, simply ensure that the appropriate class and/or ID is used on this element instead.

Scaling
-------

The current implementation of the map is based on a 494x715 SVG canvas, and so map levels must be scaled with this limitation in mind. In practice, a level's SVG elements should be no wider than 420 pixels so as to allow room for the map interface controls. It's worth mentioning that whilst Adobe Illustrator features native SVG support, and can save/export directly to SVG, it sometimes retains attributes such as `transform` which can affect the compositing of the level. For example, a layer in the document can appear to be at the origin (x: 0, y: 0) when in fact the points representing it are offset significantly, but with a negative transform. To correct these errors, you may need to use another tool to re-export the SVG (Sketch for Mac OS X does a good job at this) without the transformation.
