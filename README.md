Responsive Metadata XMP standard (First Draft)
==============================================

This standard should cover all required data needed to dynamically crop
and scale images for different output devices. The main use case is responsive
websites. Have a look at the [example xml](./example.xml).


## Namespace
The namespace for this standard is `http://universalimages.github.io/rmd/0.1/`

The preferred prefix is *rmd*.

## Representation

### Responsiveness

<table>
  <tr><th>Field Name</th><th>Value Type</th>
    <th>Optional</th><th>Description</th>
  </tr>
  <tr><td>rmd:AppliedToDimensions</td><td>Dimensions</td><td>no</td>
    <td>Width and height of the upright image (px) at the time of processing when storing responsive metadata.</td>
  </tr>
  <tr><td>rmd:AllowedDerivates</td><td>AllowedDerivates</td><td>yes</td>
    <td>Lists the allowed operations.</td>
  </tr>
  <tr><td>rmd:PivotPoint</td><td>XMP Area Point</td><td>yes</td>
    <td>All dynamic crop operations should be relative to this point.</td>
  </tr>
  <tr><td>rmd:CropArea</td><td>FrameStruct</td><td>yes</td>
    <td>Specifies the outer limits of the visible area. The outer area is considered the bleed. If this tag contains area information, the image should be cropped to those values otherwise the full image is used. The outer part (bleed) should only be used if the target aspect ratio differs from the source aspect ratio.<br>
    The `rmd:MinWidth` tag must be set if interpolation is set to `linear` so that it is clear when to start cropping. This is the only allowed tag apart from the `stArea` tags.</td>
  </tr>
  <tr><td>rmd:SafeArea</td><td>FrameStruct</td><td>yes</td>
    <td>An area that cannot be cropped into. This area holds the relevant information within the image.<br>
    **The `rmd:MaxWidth` tag must be set to define at which point this region will be used. This is the only allowed tag apart from the `stArea` tags.
    </td>
  </tr>
  <tr><td>rmd:RecommendedFrames</td><td>Bag of FrameStruct</td><td>yes</td>
    <td>A list of recommended crop regions for different output sizes. Those are only considered if `Interpolation` is set to `step`.</td>
  </tr>
  <tr><td>rmd:Interpolation</td><td>Closed Choice</td><td>yes</td>
    <td>Specifies how intermediate sizes should be treated. The following values are allowed:<br/>
    <dl>
      <dt>linear</dt><dd>Cropping can happen between markers</dd>
      <dt>step</dt><dd>Cropping only on markers</dd>
    </dl>
    </td>
  </tr>
</table>

### Rules
- `PivotPoint` has to be within `SafeArea` and `CropArea` (if defined).
- `SafeArea` has to be within `CropArea` and all `RecommendedFrames`.

The default behavior for cropping should be the following:<br>
- If the container element is larger or equal to the `CropArea` (or the width of the image), then the image must not be cropped.
- If the container element is smaller or equal to the `MaxWidth` value of the `SafeArea`, then the image must be cropped to the safe area only.
- If the `SafeArea` is not defined, then only one axis will be cropped (past the crop defined by the `CropArea`).
- If the container size is in between the two, then the behavior depends on the `Interpolation` field. If it is set to `linear` and the `MaxWidth` is set on the `SafeArea` tag, then the target width must be in between the `SafeArea` and the `CropArea`.

 If the `Interpolation` is set to `step`, then the closest matching recommended crop should be chosen. If it is not defined, then `step` should be assumed.

All values have to be normalized to dp for the calculations.

### AllowedDerivates
Currently there is only one rule:
<table>
  <tr><th>Field Name</th><th>Value Type</th><th>Description</th></tr>
  <tr><td>rmd:Crop</td><td>Closed Choice</td>
    <td>This attribute controls the level of modification allowed by the license restrictions of the image.<br />
    The following values are allowed:
    <dl>
      <dt>none</dt><dd>No cropping allowed at all.<sup>1</sup></dd>
      <dt>visibilityOnly</dt>
        <dd>This allows cropping only to prevent the visibility of details within an image. Editorial integrity must not be compromised.</dd>
      <dt>all</dt>
        <dd>This allows cropping for layout purposes as well.</dd>
    </dl>
    </td>
  </tr>
</table>

<sup>1</sup> The `CropArea` rule must still be applied if it was set. But since there is no cropping allowed, it doesn't make any sense to set it in the first place.


### FrameStruct
Extends XMP Rectangle Area. The Fields are using the `stArea` and `rmd` namespaces.

<table>
  <tr><th>Field Name</th><th>Value Type (Unit)</th>
  <th>Optional</th><th>Description</th></tr>
  <tr><td>stArea:x</td><td>Real (relative)</td><td>no<sup>1</sup></td>
  <td>X coordinate of the center of the area (point, rectangle)</td></tr>
  <tr><td>stArea:y</td><td>Real (relative)</td><td>no<sup>1</sup></td>
  <td>Y coordinate of the center of the area (point, rectangle)</td></tr>
  <tr><td>stArea:w</td><td>Real (relative)</td><td>no<sup>1</sup></td><td>Width of the area (rectangle)</td></tr>
  <tr><td>stArea:h</td><td>Real (relative)</td><td>no<sup>1</sup></td><td>Height of the area (rectangle)</td></tr>

<tr><td>rmd:MinAspectRatio</td><td>Real (dp)</td><td>yes<sup>2</sup></td>
  <td>Allows to specify the minimum aspect ratio for which this region can be used.</td>
</tr>
<tr><td>rmd:MaxAspectRatio</td><td>Real (dp)</td><td>yes<sup>2</sup></td>
  <td>Allows to specify the maximum aspect ratio for which this region can be used.</td>
</tr>
<tr><td>rmd:MinWidth</td><td>Real (dp)</td><td>yes</td>
  <td>Allows to set a minimum with (in dp) for this region to be considered.</td>
</tr>
<tr><td>rmd:MaxWidth</td><td>Real (dp)</td><td>yes<sup>2</sup></td>
  <td>Allows to set a maximum with (in dp) for this region.</td>
</tr>
</table>

<sup>1</sup>The Area fields are optional for the Default Crop Area.<br>
<sup>2</sup>Either MaxWidth or the AspectRatio fields must be specified for a frame to be considered.



## Units
  - Pixel (px): A single pixel of an image file.
  - Density independent Pixel (dp), aka CSS Pixel: This pixel is defined by a viewing angle and is independent of the physical medium. It is defined in the
  [CSS 2.1 spec](http://www.w3.org/TR/2011/REC-CSS2-20110607/syndata.html#length-units) and is around 12.8' or "the visual angle of one pixel on a device with a pixel density of 96dpi and a distance from the reader of an arm's length".
  - Relative value: A real number between 0 and 1 whereas 1 stands for the full width or height of the media file.

The point (0, 0) is the top left point relative to the **upright** image. (After Exif rotation is applied.)

## Value Types

### XMP Dimension
The field namespace URI is http://ns.adobe.com/xap/1.0/sType/Dimensions# and the prefix is `stDim`.
<table>
  <tr><th>Field Name</th><th>Value Type</th><th>Description</th></tr>
  <tr>
    <td>stDim:w</td><td>Integer</td>
    <td>Width of the image</td>
  </tr>
  <tr>
    <td>stDim:h</td><td>Integer</td>
    <td>Height of the image</td>
  </tr>
  <tr>
    <td>stDim:unit</td><td>Closed Choice</td>
    <td>Currently, `pixel` is the only allowed value.</td>
  </tr>
</table>

Pixel refers to the actual pixel value of the image.


### XMP Area
For reference, the XMP Area definition which is part of the XMP namespace is described here.
This structure represents an area. Similar to Dimensions (stDim) the “unit” field describes the specific unit being used. This is based on the definition of Exif SubjectArea. The following table gives an overview of the different types and properties being used.

<table>
  <tr><th>Type</th><th>Definition</th></tr>
  <tr><td>Point</td><td>Single point at stArea:x, stArea:y</td></tr>
  <tr><td>Rectangle</td><td>Center at stArea:x, stArea:y with bounds stArea:w, stArea:h</td></tr>
</table>

The field namespace URI is http://ns.adobe.com/xmp/sType/Area#

The preferred field namespace prefix is `stArea`.

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

## Prototype Implementations
### Adding metadata to an image
- [Adobe Metadata UI Extension](https://github.com/universalimages/rmd-extension): Allows editing the XMP metadata using the 'File Info' dialog in Adobe Photshop.
- [Universal Images Marker Plugin](https://www.adobeexchange.com): A Photoshop extension that allows the user to display and set the crop regions on the actual image.

### Processing the metadata
- [Universal Images Filter for Thumbor](https://github.com/sbaechler/thumbor-universalimages): A reference implementation in Python for a web service that can handle images with embedded responsive metadata.

- [Thumbor Universal Image Prototype](https://github.com/sbaechler/thumbor-rmd-demo): The dependencies required to set up an image server running Thumbor and Thumbor-Universalimages.
