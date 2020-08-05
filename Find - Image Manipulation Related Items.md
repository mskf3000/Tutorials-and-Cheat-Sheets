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

#### The process for any JPEG, PNG, PSD or TIFF images.

Dry run to find any JPEG, PNG, PSD or TIFF images and just echo their full path:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    echo "${FULL_IMAGE_PATH}"
	  done

Convert any JPEG, PNG, PSD or TIFF images found into 300 pixels wide or high JPEG thumbnail images at 90% quality:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|PSD|TIF|TIFF)$' |\
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

Strip out all image EXIF data from JPEG images with ExifTool:

	find 'Desktop/Pics' -type f -name '*.jpg' |\
	  while read FULL_IMAGE_PATH
	  do
	    exiftool -all= -overwrite_original_in_place "${FULL_IMAGE_PATH}"
	  done

Strip out all image EXIF data connected to JPEG, PNG or TIFF images with ExifTool:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    exiftool -all= -overwrite_original_in_place "${FULL_IMAGE_PATH}"
	  done

Strip out all keywords from JPEG images with ExifTool:

	find 'Desktop/Pics' -type f -name '*.jpg' |\
	  while read FULL_IMAGE_PATH
	  do
	    exiftool -keywords= -overwrite_original_in_place "${FULL_IMAGE_PATH}"
	  done

Strip the ICC profile out of the EXIF data connected to TIFF images with ExifTool:

	find 'Desktop/Pics' -type f -name '*.tif' |\
	  while read FULL_IMAGE_PATH
	  do
	    exiftool -all= -overwrite_original_in_place "${FULL_IMAGE_PATH}"
	  done

Strip the ICC profile out of the EXIF data connected to JPEG, PNG or TIFF images with ExifTool:

	find 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    exiftool -icc_profile:all= -overwrite_original_in_place "${FULL_IMAGE_PATH}"
	  done

Strip out all image EXIF data from JPEG, PNG or TIFF images with ExifTool:

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

### Set the 'Creator' value to a bunch of JPEG images.

Change the `Creator` value to match whatever creator value you want:

	find 'Desktop/Pics' -type f -name '*.jpg' |\
	  while read FULL_IMAGE_PATH
	  do
	    exiftool -Creator='Jack Szwergold' -overwrite_original_in_place "${FULL_IMAGE_PATH}"
	  done

### Change the DPI (density) of images.

Change the DPI of JPEG images to 200 dpi:

	find 'Desktop/Pics' -type f -name '*.jpg' |\
	  while read FULL_IMAGE_PATH
	  do
	    convert -density 200 -units PixelsPerInch -quality 100 "${FULL_IMAGE_PATH}" "${FULL_IMAGE_PATH}"
	  done

### Resize images.

Resize JPEG, PNG, PSD, TIFF or HEIC images to dimensions of 1200 pixels wide or high if they are larger than 1200 pixels:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|PSD|TIF|TIFF|HEIC)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    convert -density 72 -units PixelsPerInch -resize "1200x1200>" -quality 100 "${FULL_IMAGE_PATH}" "${FULL_IMAGE_PATH}"
	  done

Resize JPEG, PNG, TIFF or HEIC images to dimensions of 1920 pixels wide or high if they are larger than 1920 pixels:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF|HEIC)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    convert -density 72 -units PixelsPerInch -resize "1920x1920>" -quality 90 "${FULL_IMAGE_PATH}" "${FULL_IMAGE_PATH}"
	  done

### Convert JPEG, PNG, TIFF or HEIC images to JPEG images at 90% quality.

This command converts JPEG, PNG, TIFF or HEIC images to JPEGs at 90% quality:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF|HEIC)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    PATH_SANS_EXTENSION="${FULL_IMAGE_PATH%.*}"
	    convert -density 72 -units PixelsPerInch -quality 90 "${FULL_IMAGE_PATH}" "${PATH_SANS_EXTENSION}".jpg
	  done

This command converts TIFF images to JPEGs at 100% quality with a 1200 DPI setting:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF|HEIC)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    PATH_SANS_EXTENSION="${FULL_IMAGE_PATH%.*}"
	    convert -density 1200 -units PixelsPerInch -quality 100 "${FULL_IMAGE_PATH}" "${PATH_SANS_EXTENSION}".jpg
	  done

### Converting JPEG, PNG and TIFF images to HEIC files.

This command converts images to HEIC format:

	find -E 'Desktop/Pics' -type f -iregex '.*\.(JPG|JPEG|PNG|TIF|TIFF)$' |\
	  while read FULL_IMAGE_PATH
	  do
	    PATH_SANS_EXTENSION="${FULL_IMAGE_PATH%.*}"
	    convert "${FULL_IMAGE_PATH}" "${PATH_SANS_EXTENSION}".heic
	    exiftool -overwrite_original_in_place -tagsFromFile "${FULL_IMAGE_PATH}" "${PATH_SANS_EXTENSION}".heic
	  done

### Convert raster images to vector images using Potrace.

This script is a first draft of a script that uses ImageMagick and Potrace to convert rasterized images into vector images. Works fine so far, but it would be nice to simply stream the output of convert right into potrace.

	find -E . -type f -iregex ".*\.(JPG|JPG|PNG|TIF|TIFF)$" |\
	  while read full_image_filepath
	  do
	
	    # Break up the full  filepath stuff into different directory and filename components.
	    image_dirname=$(dirname "${full_image_filepath}");
	    image_basename=$(basename "${full_image_filepath}");
	    image_filename="${image_basename%.*}";
	    # image_extension="${image_basename##*.}";
	
	    # Set the directory values.
	    eps_dirpath="${image_dirname}/eps";
	    pnm_dirpath="${image_dirname}/eps";
	
	    # Set the filename values.
	    eps_filepath="${eps_dirpath}/${image_filename}.eps";
	    pnm_filepath="${pnm_dirpath}/${image_filename}.pnm";
	
	    # Create the child directories.
	    mkdir -p "${eps_dirpath}";
	
	    # Where the magic happens.
	    convert "${full_image_filepath}" "${pnm_filepath}";
	    potrace --eps "${pnm_filepath}" -o "${eps_filepath}";
	    rm -f "${pnm_filepath}";
	
	  done


***

*Find - Image Manipulation Related Items (c) by Jack Szwergold; written on September 29, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*