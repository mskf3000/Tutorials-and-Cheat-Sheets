## ImageMagick

By Jack Szwergold

### Basics of image conversion.

Recompress a JPEG image at 95 percent quality:

	convert "Source/test.jpg" -quality 95 "Destination/test.jpg"

Recompress a JPEG image at 95 percent quality (`-quality 95`) and force the processing colorspace to RGB (`-colorspace sRGB`):

	convert "Source/test.jpg" -quality 95 -colorspace sRGB "Destination/test.jpg"

Recompress a JPEG image at 95 percent quality (`-quality 95`), force the processing colorspace to RGB (`-colorspace sRGB`) and sharpen it by 1 pixel (`-sharpen 0x1`):

	convert "Source/test.jpg" -quality 95 -colorspace sRGB -sharpen 0x1 "Destination/test.jpg"

### Resize an image.

Resize an image at 720 pixels wide:

    convert "Source/test.jpg" -resize 720x "Destination/test.jpg"

Resize an image at 720 pixels wide only if the width is already greater than 720 pixels wide:

    convert "Source/test.jpg" -resize 720x\> "Destination/test.jpg"

Resize an image at 720 pixels high:

    convert "Source/test.jpg" -resize x720 "Destination/test.jpg"

Resize an image at 720 pixels high only if the height is already greater than 720 pixels wide:

    convert "Source/test.jpg" -resize x720\> "Destination/test.jpg"

Resize an image at 720 pixels wide by 720 pixels high based on the longest dimension:

    convert "Source/test.jpg" -resize 720x720 "Destination/test.jpg"

Resize an image at 720 pixels wide by 720 pixels high based on the longest dimension only if that dimension is already greater than 720 pixels:

    convert "Source/test.jpg" -resize 720x720\> "Destination/test.jpg"

### Density and units.

Convert an images DPI (`-density 300`) and make sure the units are pixels per inch (`-units PixelsPerInch`):

    convert "Source/test.jpg" -density 300 -units PixelsPerInch "Destination/test.jpg"

### Getting the color of an image.

This get the RGB color value based on using `-resize`:

    convert test.png -resize 1x1\! -depth 8 txt:- | tail -1

The returned value would be something like this:

    0,0: (185,200,201)  #B9C8C9  srgb(185,200,201)

This get the RGB color value based on using `-scale`:

    convert test.png -scale 1x1\! -depth 8 txt:- | tail -1

The returned value would be something like this:

    0,0: (185,200,200)  #B9C8C8  srgb(185,200,200)

This get the RGB color value based on using `-colors`:

    convert test.png -colors 1 -depth 8 txt:- | tail -1

The returned value would be something like this:

    80,105: (185,200,200)  #B9C8C8  srgb(185,200,200)

This get the grayscale value based on using `-colorspace GRAY`:

    convert test.png -colorspace GRAY -colors 1 -depth 8 txt:- | tail -1

The returned value would be something like this:

    80,105: (197,197,197)  #C5C5C5  gray(197)

This get the CMYK color value based on using `-colorspace GRAY`:

    convert test.png -colorspace CMYK -colors 1 -depth 8 txt:- | tail -1

The returned value would be something like this:

    80,105: (185,200,200)  #B9C8C8  srgb(185,200,200)

### Generate some test images.

These commands will generate some test images at 200 pixels wide by 200 pixels high:

	convert -size 200x200 xc:red ~/Desktop/test_red.jpg
	convert -size 200x200 xc:blue ~/Desktop/test_blue.jpg
	convert -size 200x200 xc:yellow ~/Desktop/test_yellow.jpg
	convert -size 200x200 xc:green ~/Desktop/test_green.jpg
	convert -size 200x200 xc:orange ~/Desktop/test_orange.jpg
	convert -size 200x200 xc:darkgrey ~/Desktop/test_darkgrey.jpg
	convert -size 200x200 xc:purple ~/Desktop/test_purple.jpg

These commands will generate some test images at 1200 pixels wide by 400 pixels high:

	convert -size 1200x400 xc:red ~/Desktop/big_test_red_1200w.jpg
	convert -size 1200x400 xc:blue ~/Desktop/big_test_blue_1200w.jpg
	convert -size 1200x400 xc:yellow ~/Desktop/big_test_yellow_1200w.jpg
	convert -size 1200x400 xc:green ~/Desktop/big_test_green_1200w.jpg
	convert -size 1200x400 xc:orange ~/Desktop/big_test_orange_1200w.jpg
	convert -size 1200x400 xc:darkgrey ~/Desktop/big_test_darkgrey_1200w.jpg
	convert -size 1200x400 xc:purple ~/Desktop/big_test_purple_1200w.jpg

These commands will generate some test images at 1920 pixels wide by 1200 pixels high:

	convert -size 1920x1200 xc:red ~/Desktop/big_test_red_1920w.jpg
	convert -size 1920x1200 xc:blue ~/Desktop/big_test_blue_1920w.jpg
	convert -size 1920x1200 xc:yellow ~/Desktop/big_test_yellow_1920w.jpg
	convert -size 1920x1200 xc:green ~/Desktop/big_test_green_1920w.jpg
	convert -size 1920x1200 xc:orange ~/Desktop/big_test_orange_1920w.jpg
	convert -size 1920x1200 xc:darkgrey ~/Desktop/big_test_darkgrey_1920w.jpg
	convert -size 1920x1200 xc:purple ~/Desktop/big_test_purple_1920w.jpg

***

*ImageMagick (c) by Jack Szwergold; written on October 5, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*

