## Iconv

By Jack Szwergold

***

The overall format of using Incov from the command line:

	iconv -f old-encoding -t new-encoding file.txt > file_converted.txt

An exmaple of converting a file from `UTF-8` to `iso-8859-1`:

	iconv -f UTF-8 -t iso-8859-1 file.txt > file_converted.txt

***

*Iconv (c) by Jack Szwergold; written October 7, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*