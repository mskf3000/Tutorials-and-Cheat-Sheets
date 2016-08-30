## Find - Image Manipulation Related Items

By Jack Szwergold

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
	    PATH_SANS_EXTENSION="${FULL_IMAGE_PATH%.*}"
	    convert -density 72 -units PixelsPerInch -quality 90 -resize "300x300>" "${FULL_IMAGE_PATH}" "${PATH_SANS_EXTENSION}"_t.jpg
	  done

#### The process for any JPEG, PNG or TIFF images.

Dry run to find any JPEG, PNG or TIFF images and just echo their full path:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    echo "${FULL_IMAGE_PATH}"
	  done

Convert any JPEG, PNG or TIFF images found into 300 pixels wide or high JPEG thumbnail images at 90% quality:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    PATH_SANS_EXTENSION="${FULL_IMAGE_PATH%.*}"
	    convert -density 72 -units PixelsPerInch -quality 90 -resize "300x300>" "${FULL_IMAGE_PATH}" "${PATH_SANS_EXTENSION}"_t.jpg
	  done

#### The process to convert any JPEG, PNG or TIFF images into a thumbnail that matches the original format extension.

Dry run to find any JPEG, PNG or TIFF images and just echo their full path:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    echo "${FULL_IMAGE_PATH}"
	  done

Convert any JPEG, PNG or TIFF images found into a same format 300 pixels wide or high thumbnail image at 90% quality:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    # PATH_SANS_EXTENSION="${FULL_IMAGE_PATH%.*}"
	    # Parse the directory name, extension & filename.
        DIRNAME=$(dirname "${FULL_IMAGE_PATH}")
        BASENAME=$(basename "${FULL_IMAGE_PATH}")
        FILENAME="${BASENAME%.*}"
        EXTENSION="${BASENAME##*.}"
	    convert -density 72 -units PixelsPerInch -quality 90 -resize "300x300>" "${FULL_IMAGE_PATH}" "${DIRNAME}"/"${FILENAME}"_t."${EXTENSION}"
	  done

### Strip out EXIF data with ExifTool.

Strip out all image EXIF data from JPEG images with ExifTool to:

	find 'Desktop/Pics' -type f -name '*.jpg' |\
	  while read FULL_IMAGE_PATH
	  do
	    exiftool -all= -overwrite_original_in_place "${FULL_IMAGE_PATH}"
	  done

Strip the ICC profile out of the EXIF data connected to JPEG images with ExifTool to:

	find 'Desktop/Pics' -type f -name '*.jpg' |\
	  while read FULL_IMAGE_PATH
	  do
	     exiftool -icc_profile:all= -overwrite_original_in_place "${FULL_IMAGE_PATH}"
	  done

Strip the ICC profile out of the EXIF data connected to TIFF images with ExifTool to:

	find 'Desktop/Pics' -type f -name '*.tif' |\
	  while read FULL_IMAGE_PATH
	  do
	    exiftool -icc_profile:all= -overwrite_original_in_place "${FULL_IMAGE_PATH}"
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

Resize JPEG, PNG or TIFF images to dimensions of 1000 pixels wide or high if they are larger than 1000 pixels:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    convert -density 72 -units PixelsPerInch -resize "1000x1000>" -quality 90 "${FULL_IMAGE_PATH}" "${FULL_IMAGE_PATH}"
	  done

Resize JPEG, PNG or TIFF images to dimensions of 1500 pixels wide or high if they are larger than 1500 pixels:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    convert -density 72 -units PixelsPerInch -resize "1500x1500>" -quality 90 "${FULL_IMAGE_PATH}" "${FULL_IMAGE_PATH}"
	  done

### Convert TIFF images to JPEG images at 90% quality.

This command simply converts TIFF images to JPEGs at 90% quality:

	find 'Desktop/Pics' -type f -name '*.tif' |\
	  while read FULL_IMAGE_PATH
	  do
	    PATH_SANS_EXTENSION="${FULL_IMAGE_PATH%.*}"
	    convert -density 72 -units PixelsPerInch -quality 90 "${FULL_IMAGE_PATH}" "${PATH_SANS_EXTENSION}".jpg
	  done

***

*Find - Image Manipulation Related Items (c) by Jack Szwergold; written on September 29, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*