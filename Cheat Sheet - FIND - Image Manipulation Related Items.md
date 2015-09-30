# Cheat Sheet - FIND - Image Manipulation Related Items

By Jack Szwergold, September 29, 2015

### Convert images into a JPEG tumbnail.

#### The process for just JPEG images.

Dry run to find any JPEG images and just echo their full path:

	find 'Desktop/Pics' -type f -name '*.jpg' |\
	  while read FULL_IMAGE_PATH
	  do
	    echo "${FULL_IMAGE_PATH}"
	  done

Convert any JPEG images found into JPEG thumbnail images at 90% quality:

	find 'Desktop/Pics' -type f -name '*.jpg' |\
	  while read FULL_IMAGE_PATH
	  do
	    FILENAME="${FULL_IMAGE_PATH%.*}"
	    convert -density 72 -units PixelsPerInch -quality 90 "${FULL_IMAGE_PATH}" "${FILENAME}"_t.jpg
	  done

#### The process for any JPEG, PNG or TIFF images.

Dry run to find any JPEG, PNG or TIFF images and just echo their full path:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    echo "${FULL_IMAGE_PATH}"
	  done

Convert any JPEG, PNG or TIFF images found into JPEG thumbnail images at 90% quality:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    FILENAME="${FULL_IMAGE_PATH%.*}"
	    convert -density 72 -units PixelsPerInch -quality 90 "${FULL_IMAGE_PATH}" "${FILENAME}"_t.jpg
	  done

### Strip out EXIF data with ExifTool.

Strip out all image EXIF data from JPEG images with ExifTool to:

	find 'Desktop/Pics' -type f -name '*.jpg' |\
	  while read FULL_IMAGE_PATH
	  do
	    exiftool -all= -overwrite_original_in_place "${FULL_IMAGE_PATH}"
	  done

Strip out all image EXIF data from TIFF images with ExifTool to:

	find 'Desktop/Pics' -type f -name '*.tif' |\
	  while read FULL_IMAGE_PATH
	  do
	    exiftool -icc_profile:all= -overwrite_original_in_place "${FILENAME}"
	  done

Strip out all image EXIF data from JPEG, PNG or TIFF images with ExifTool to:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    exiftool -all= -overwrite_original_in_place "${FULL_IMAGE_PATH}"
	  done

Find any stray images with the `*.jpg_original` extension that ExifTool created and remove them:

	find 'Desktop/Pics' -type f -name '*.jpg_original' |\
	  while read FULL_IMAGE_PATH
	  do
	    rm "${FULL_IMAGE_PATH}"
	  done

### Change the DPI (density) of images.

Change the DPI of JPEG images to 200 dpi:

	find 'Desktop/Pics' -type f -name '*.jpg' |\
	  while read FULL_IMAGE_PATH
	  do
	    convert -density 200 -units PixelsPerInch -quality 100 "${FULL_IMAGE_PATH}" "${FULL_IMAGE_PATH}"
	  done

### Resize images.

Resize JPEGs to dimensions of 1000 pixels wide or high if they are larger than 1000 pixels:

	find 'Desktop/Pics' -type f -name '*.jpg' |\
	  while read FULL_IMAGE_PATH
	  do
	    convert -density 72 -units PixelsPerInch -resize "1000x1000>" -quality 90 "${FULL_IMAGE_PATH}" "${FULL_IMAGE_PATH}"
	  done

Resize JPEGs to dimensions of 1500 pixels wide or high if they are larger than 1500 pixels:

	find 'Desktop/Pics' -type f -name '*.jpg' |\
	  while read FULL_IMAGE_PATH
	  do
	    convert -density 72 -units PixelsPerInch -resize "1500x1500>" -quality 90 "${FULL_IMAGE_PATH}" "${FULL_IMAGE_PATH}"
	  done

***

*Cheat Sheet - FIND - Image Manipulation Related Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*