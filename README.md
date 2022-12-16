# Documentation

# Run Instructions

### To retrieve single-band data:

1. Go to [https://earthexplorer.usgs.gov/](https://earthexplorer.usgs.gov/)
2. Search for the desired location and date range
3. Select the dataset ‘Landsat 8-9 OLI/TIRS C2 L2’
4. Export the bands B2, B3, B4 (blue, green, red as described in the [Data Format Controls Book](https://d9-wret.s3.us-west-2.amazonaws.com/assets/palladium/production/s3fs-public/atoms/files/LSDS-1328_Landsat8-9-OLI-TIRS-C2-L2-DFCB-v6.pdf)) and the metadata files (.txt files)
5. Save to your desired location

### To retrieve a multispectral image:

I wasn’t that sure how to do this so I just pulled some from [arcgis](https://www.arcgis.com/apps/mapviewer/index.html) by:

1. Selecting an area on the map
2. Exporting at the desired size into a TIFF format

### To use the notebooks:

Change the ‘img’ (for multispectral) or ‘prefix’ for (single band) to match the files that were exported.

- Block 2, Line 1 of ‘multispectral’
- Block 3, Line 1 of ‘single band’

# Process Explanation

In this section, I will go through all of the requirements that were set, whether I felt I completed them or not, and any questions, challenges or assumptions with each.

1. High resolution images are presented for viewing as [Cloud Optimised Geotiffs](https://www.cogeo.org/)
    - I don’t feel like I completed this as I wasn’t sure how to ‘cloud optimise’ and ran out of time to find out really
    - From my research, I think this involves using the `gdal` library and making sure that the output is tiled and compressed to make it suitable for cloud environments

1. High resolution images are “georeferenced”, i.e. have geolocation information included
    - From my understanding, the ‘georeference’ is the profile of the tif file which can be accessed using
    
    ```python
    image.profile 
    ```
    
    - if the image is a .tif file opened with `rasterio` or `gdal` etc.
    - An example of this can be seen in the ‘single band’ notebook under the ‘georeferencing’ heading

1. For a multi spectral data source:
    - Each “band” is represented by a single-channel high resolution image
    - There is a “true colour” three-channel asset, where R, G and B bands are available

- I felt I completed this one to an extent:
    - I used pyplot to present the RGB bands for the same multispectral image
    - I also overlayed RGB using np.stack to create a true colour image in the ‘single band’ notebook
- The part that I ran out of time for was creating a true colour image of the multispectral geoTIFF, but I think this would be somewhat similar to overlaying in the ‘single band’ notebook

1. If an image contains pixels where no data were captured, these are rendered transparent
    - I didn’t get time for this, but I assume it would be best to use the ImageMagick library and use one of the transparency features
    - Another thing I thought of was maybe replacing any of the 0s in the GeoTiff array with a NoneType or False or something (not sure how this would render though)

1. An overview image is given, in webp format, no more than 500 kB in size
    - I didn’t do this - I wasn’t exactly sure how to find the existing size of the file outside of Path and decided to prioritise other tasks

1. A thumbnail is given, in webp format, no more than 500 px wide or high
- I initially tried to use gdal here by:
    - create an output file using gdal.Create with the new dimensions
    - resampling the image
    - translating the resampled image to a WEBP driver but kept encountering this error:
        
        ![Untitled](Documentation%2097336ef917a14f039c96482e4fccbf66/Untitled.png)
        
- I then tried to resample with rasterio using the Resampling class and example from the documentation, but realised this just changed the cells and not the pixels

1. Metadata files included with the source are also included, as unchanged from the original as possible
- I wasn’t exactly sure what this metadata was, but I assume it’s the .txt files that come with the images from e.g. Earth Explorer
- I assume this part of the challenge is where you extract all the images and files created above, and make sure that the correct metadata files are included, but I ran out of time for that

# General Learnings

- understanding how to search for satellite data and the format that it comes in
- what the differences between [collections/levels/tiers](https://d9-wret.s3.us-west-2.amazonaws.com/assets/palladium/production/s3fs-public/atoms/files/Landsat-C1vsC2-2021-0430-LMWS.pdf) are for Landsat data
- what metadata is attached to geotiff images and how to access it
- using colour maps in plotly
- Understanding raster images and their array format
- What SPAC is, and what an item that could be cataloged actually looks like
- Some of the features of Rasterio and Gdal

# Next steps

- It would be better to load data from an API such as [Open Data Cube](https://datacube-core.readthedocs.io/en/latest/index.html), rather than having to manually load data
- Being able to automatically export all of the files and images created in the notebooks into a folder for each dataset (inc different band images, metadata etc.)
- Try using the ImageMagick library to do the transparency requirement
- Cloud-optimising the GeoTIFFs so that they can actually be hosted on a cloud platform and made accessible to customers
- Adding to DataCosmos’ STAC catalog