# Cheat Sheet - ImageMagick

By Jack Szwergold, October 5, 2015

#### Basics of image conversion.

Recompress a JPEG image at 95 percent quality:

	convert "Source/test.jpg" -quality 95 "Destination/test.jpg"

Recompress a JPEG image at 95 percent quality (`-quality 95`) and force the processing colorspace to RGB (`-colorspace sRGB`):

	convert "Source/test.jpg" -quality 95 -colorspace sRGB "Destination/test.jpg"

Recompress a JPEG image at 95 percent quality (`-quality 95`), force the processing colorspace to RGB (`-colorspace sRGB`) and sharpen it by 1 pixel (`-sharpen 0x1`):

	convert "Source/test.jpg" -quality 95 -colorspace sRGB -sharpen 0x1 "Destination/test.jpg"

#### Resize an image.

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

#### Density and units.

Convert an images DPI (`-density 300`) and make sure the units are pixels per inch (`-units PixelsPerInch`):

    convert "Source/test.jpg" -density 300 -units PixelsPerInch "Destination/test.jpg"

***

*Cheat Sheet - ImageMagick (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*

