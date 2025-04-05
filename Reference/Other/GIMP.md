#gimp 

## General

- Drag in from the guides to show a guide bar through your image
- If I can convert a selection to/from a mask I can easily edit a selection while viewing the transparency level.
- To isolate hair from the background:

1. Duplicate the layer.
2. Crank the contrast by drawing an "S" shape in Curves tool.
3. Desaturate.
4. Create this as a layer mask on the original layer.
5. To clean up edges that bleed into background just use the Clone Stamp tool.  Duh, so obvious!

- It often makes sense to create and modify a new layer just for the purpose of creating a complex selection that you will use later.

## Tools

- Rectangle Select tool allows rounded corners
- Ellipse select tool lets you grow from centre, or use a fixed ascpect ratio to draw circles.
- Scissors Select tool lets you click on the edges of a selection and it intelligently tries to fill in the rest of the edge.  You can create a point anywhere along the line it draws to adjust.  In many ways it's like lasso but more automated.
- Foreground Select tool lets you scribble in the interior of a selection and it intelligently expands that out to select the rest of the shape.
- Healing tool is similar to Clone Stamp except it blends the source and destination together.  Can make for a weird "mixed paint" effect if the destination has any irregularities.  But if destination is smooth it's really quite good.

## Layers

- Layer masks also use shades of grey
- Applying a layer mask will remove the mask and apply the transparency
- You can change the layer (overlay) mode to have a layer modify the layer under it.
- Clicking the link icon on layers simply allows any operation to affect all linked layers at once.

## Channels

- You need to have an Alpha channel on your layer to use transparency
- Save a selection to a channel to be able to re-use that selection later and with other layers.  Use Selection to Channel to save it and Channel to Selection to re-use it.
- Channels are really just a way to save a selection.
- Selections support transparency.  Channels support transparency.  Really, both are just your image with every pixel represented by a single number from 0-255.  So channels and selections are the same thing.  RGB and A are just default channels with specific uses.  Masks are the same thing. 

## Colours

- Adjusting the curves affects (Left to Right) shadows, mid-tones, highlights
- Dragging the curve up highlights that colour in that range
- Dragging the curve down mutes that colour in that range
- To highlight a non-primary colour, find its opposite on the colour wheel and mute that colour
- When desaturating an image, Luminosity is generally the method that most closely emulates the brightness level we perceive in a photo.
- You can also use Channel Mixer --> Monochrome to convert a photo to B&W.  It lets you manually choose how each colour channel affects the outcome, instead of just using presets.
- In general use Preserve Luminosity to avoid effects when you adjust variables beyond their max setting.
- Thresholds are used to make something either black or white, or transparent or opaque based on a threshold setting.  Probably great for creating layer masks.