## Ghostscript

By Jack Szwergold

### Convert a directory of EPS files into a PDF.

Be sure GhostScript is installed, manuever into the directory that contains the EPS files and run this command. The output will be saved to a PDF named

	
	gs \
	  -sDEVICE=pdfwrite -dNOPAUSE -dBATCH -dSAFER \
	  -dFIXEDMEDIA -dEPSFitPage \
	  -dPDFSETTINGS=/printer \
	  -dCompatibilityLevel=1.3 \
	  -dEmbedAllFonts=true -dSubsetFonts=true \
	  -dColorImageDownsampleType=/Bicubic \
	  -dColorImageResolution=144 \
	  -dGrayImageDownsampleType=/Bicubic \
	  -dGrayImageResolution=144 \
	  -dMonoImageDownsampleType=/Bicubic \
	  -dMonoImageResolution=144 \
	  -sOutputFile=output.pdf \
	*.eps


***

*Ghostscript (c) by Jack Szwergold; written on January 29, 2022. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*