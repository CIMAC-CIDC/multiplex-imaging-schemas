# multiplex-imaging-schemas
A library proposing standards for harmonized acquisition of multiplex imaging commonly used in immuno-profiling.

## Summary
The following is a living document, essentially an RFC, around how to best model the various data associated with multiplex imaging technologies used for immuno-profiling. Please comment by submitting a PR requesting changes to the current master document.

### status
This document is completely under consutruction and not ready for comments.

# Components
The following sections detail components of multiplex imaging experiments.

## image-stack
The image stack is a set of images, represented as an nxWxH matrix where **n** is the number of channels measured, and **W,H** are the width and height of each channel. It is assumed that every channel in the image is "registered" and the content of each channel represents a different measurement of the same underlying cells.

**proposal:** A given image stack is saved in a harmonized format.

- The standardized OME-TIFF file where the image can be loaded into common commercial and open-source imaging applications, the channel names and prefered colors are preserved if possible.

## aquisition-properties
These are meta-data associated with the aquisition of the image. These can include variables such as the zoom, the pixel density, exposure time, dwell time etc...

**proposal:** Identify required / common parameters which are necessary to properly quantify the image, or display them in common viewers, and make these required. Then we provide a mechnaism to submit other parameters with no control.
```
required = {
  "pixels_per_cm": int,
  "channel_names": [str,*],
  "channel_colors_rgb": [str,*],
}
```

## technical-masking
This is a way to annotate which pixels in the **image-stack** are to be included in any subsequent analysis. This masking is technical in nature and the areas excluded should be irrelavent to the analysis because they do not meet some quality check, have experimental artifacts, or are simply not applicable. The intent of this data is not to select or exclude biologically relavent areas of interest.

**proposal:** A set of binary arrays matching the height and width of the source image. A pointer to the source image. A small amount of meta information describing the mask i.e. how it was created, date it was created, inclusionary/exclusionary, an enum specifying the mask intent (technical artifact, low quality image, poor staining, poor xyz, not applicable tissue, etc...).

## cell-segmentation
Partitioning the **image-stack** into regions representing a single cell. The regions may be defined both as the membrane boundary and/or nuclear boundary.

**proposal:** A set of int32 arrays, the same height and width dimension of the image-stack. Each pixel should have an integter indicating which cell that pixel is part of. For each array, there should be a lookup table mapping the integer to a unique (in the project) cell identifier.


## region-masking
This is a way to annotate biologically relavent areas of the image. Each region will represent an area of the image to include or exclude in subsequent analysis. 

**proposal:** The array should be boolean and match the height/width dimensions of the **image-stack**. A small amount of meta information describing each mask i.e. how it was created, date it was created, inclusionary/exclusionary, an enum specifying the mask intent (tumor, stroma, etc...).

## roi-selection
This is a step where ROIs are selected from the source **image-stack**. The ROI selection will involved specifying a polygone of the area considered to be an ROI, along with a name of the ROI. Since the ROI may be obtained by scanning the source material at a higher resolution, we do not explicitly store pixels in this representation.

**proposal:** A set of list of polygons (in whatever format polygons can be defined ... i.e. scikit-image). Each polygon can be used to determine which of the pixels in the **image-stack** are in a given ROI. Also each ROI should have a small amount of metadata specifying how the ROI was anntoated, when and an enum marking up the purpose of the ROI.






