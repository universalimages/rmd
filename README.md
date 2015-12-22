Responsive Metadata XMP standard
================================

This standard should cover all required data needed to dynamically crop
and scale images for different output devices. The main use case is responsive
websites. Have a look at the [example implementation](./example.xml).

The preferred prefix is *rmd*.

The following data can be stored:

## Representation

### Responsiveness
<table>
  <tr><th>Field Name</th><th>Value Type</th><th>Description</th></tr>
  <tr><td>rmd:Responsiveness</td><td>ResponsiveInfos</td>
    <td>Main structure containing responsiveness based information.</td>
  </tr>
</table>


### ResponsiveInfos
<table>
  <tr><th>Field Name</th><th>Value Type</th>
    <th>Optional</th><th>Description</th>
  </tr>
  <tr><td>rmd:AppliedToDimensions</td><td>Dimensions</td><td>no</td>
    <td>Width and height of the image (px) at the time of processing when storing responsive metadata.</td>
  </tr>
  <tr><td>rmd:AllowedDerivates</td><td>Bag of AllowedDerivates</td><td>yes</td>
    <td>Lists the allowed operations.</td>
  </tr>
  <tr><td>rmd:CropArea</td><td>XMP Area Rectangle</td><td>yes</td>
    <td>Specifies the outer limits of the visible area. The outer area is considered the bleed. If this tag is present, the image should be cropped to those values. The outer part should only be used if the target aspect ratio differs from the source aspect ratio.</td>
  </tr>
  <tr><td>rmd:SafeArea</td><td>XMP Area Rectangle</td><td>yes</td>
    <td>An area that cannot be cropped into. This area holds the relevant information within the image.</td>
  </tr>
  <tr><td>rmd:MinWidth</td><td>real</td><td>yes</td>
    <td>The minimal viewport width (dp) required to show the full image. If the width is smaller, the image can be cropped.</td>
  </tr>
  <tr><td>rmd:RecommendedFrames</td><td>Bag of FrameStruct</td><td>yes</td>
    <td>A list of recommended crop regions for different output sizes</td>
  </tr>
  <tr><td>rmd:Interpolation</td><td>Closed Choice</td><td>yes</td>
    <td>Specifies how intermediate sizes should be treated. The following values are allowed:<br>
    <dl>
      <dt>linear</dt><dd>Cropping can happen between markers</dd>
      <dt>step</dt><dd>Cropping only on markers</dd>
    </dl>
    </td>
  </tr>
</table>

### AllowedDerivates
<table>
  <tr><th>Field Name</th><th>Value Type</th><th>Description</th></tr>
  <tr><td>rmd:Crop</td><td>Closed Choice</td>
    <td>This attribute controls the level of modification allowed by the license restrictions of the image.<br>

    The following values are allowed:
    <dl>
      <dt>none</dt><dd>No cropping allowed at all.</dd>
      <dt>visibilityOnly</dt>
        <dd>This allows cropping only to prevent the visibility of details within an image. Editorial integrity needs to be prevented.</dd>
      <dt>all</dt>
        <dd>This allows cropping for layout purposes as well.</dd>
    </dl>
    </td>
  </tr>
</table>

### FrameStruct
Extends XMP Rectangle Area (Requires stArea:x, stArea:y, stArea:w, stArea:h, see below)
<table>
<tr><th>Field Name</th><th>Value Type</th>
  <th>Optional</th><th>Description</th>
</tr>
<tr><td>rmd:MinAspectRatio</td><td>real</td><td>yes</td>
  <td>Allows to specify the minimum aspect ratio for which this region can be used.</td>
</tr>
<tr><td>rmd:MaxAspectRatio</td><td>real</td><td>yes</td>
  <td>Allows to specify the maximum aspect ratio for which this region can be used.</td>
</tr>
<tr><td>rmd:MinWidth</td><td>real</td><td>yes</td>
  <td>Allows to set a minimum with (in dp) for this region.</td>
</tr>
<tr><td>rmd:MaxWidth</td><td>real</td><td>yes</td>
  <td>Allows to set a maximum with (in dp) for this region.</td>
</tr>
</table>

Either MaxWidth or theAspectRatio fields must be specified for a frame to be considered.

## Units
  - Pixel (px): A single pixel of an image file.
  - Density independent Pixel (dp), aka CSS Pixel: This pixel is defined by a viewing angle and is independent of the physical medium. It is defined in the
  [CSS 2.1 spec](http://www.w3.org/TR/2011/REC-CSS2-20110607/syndata.html#length-units) and is around 12.8' or "the visual angle of one pixel on a device with a pixel density of 96dpi and a distance from the reader of an arm's length".


## Value Types

### XMP Area
For reference, the XMP Area definition which is part of the XMP namespace is described here.
This structure represents an area. Similar to Dimensions (stDim) the “unit” field describes the specific unit being used. This is based on the definition of Exif SubjectArea. The following table gives an overview of the different types and properties being used.

<table>
  <tr><th>Type</th><th>Definition></th></tr>
  <tr><td>Point</td><td>Single point at stArea:x, stArea:y</td></tr>
  <tr><td>Circle</td><td>Center at stArea:x, stArea:y with diameter stArea:d</td></tr>
  <tr><td>Rectangle</td><td>Center at stArea:x, stArea:y with bounds stArea:w, stArea:h</td></tr>
</table>

The field namespace URI is http://ns.adobe.com/xmp/sType/Area#

The preferred field namespace prefix is `stArea`.

<table>
  <tr><th>Field Name</th><th>Value Type</th><th>Description</th></tr>
  <tr><td>stArea:x</td><td>Real</td>
  <td>X coordinate of the center of the area (point, circle, rectangle)</td></tr>
  <tr><td>stArea:y</td><td>Real</td>
  <td>Y coordinate of the center of the area (point, circle, rectangle)</td></tr>
  <tr><td>stArea:w</td><td>Real</td><td>Width of the area (rectangle)</td></tr>
  <tr><td>stArea:h</td><td>Real</td><td>Height of the area (rectangle)</td></tr>
  <tr><td>stArea:d</td><td>Real</td><td>Diameter of area (circle)</td></tr>
  <tr><td>￼stArea:unit</td>￼<td>Open Choice</td>
  ￼<td>In the context of this document, only “normalized” is being specified for handling image regions.
  However, for compatibility with the XMP specification and future extensibility, the list will be kept open so that absolute coordinates could be added later-on.</td></tr>
</table>
