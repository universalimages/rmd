Responsive Metadata XMP standard (First Draft)
==============================================

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
  <tr><td>rmd:PivotPoint</td><td>XMP Area Point</td><td>yes</td>
    <td>All dynamic crop operations should be relative to this point.</td>
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

### Rules
- PivotPoint has to be within SafeArea and CropArea (if defined).
- SafeArea has to be within CropArea and all RecommendedFrames.

### AllowedDerivates
Currently there is only one rule:
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

## Compatibility with other standards
The RMD standard brings all information needed for responsive images into one namespace. It's possible that some information is already stored in different places. The following table specifies how existing metadata should be treated.

<table>
  <tr><th>Field Name</th><th>Action</th></tr>
  <tr>
    <td>SubjectArea</td>
    <td>If SubjectArea is a Point type, the value should be used as a placeholder value for rmd:PivotPoint. If it is a Rectangle type, it should be used as a placeholder for rmd:SafeArea.</td>
  </tr>
  <tr>
    <td>SubjectLocation</td>
    <td>If SubjectLocation is specified and SubjectArea is not a Point type, then SubjectLocation should be used as a placeholder value for rmd:PivotPoint.</td>
  </tr>
  <tr>
    <td>XMP-cc:License</td>
    <td>If the image contains a popular restrictive license such as the Creative Commons NC license, rmd:AllowedDerivates shall be set to false.
    </td>
  </tr>
</table>
