## Awk

By Jack Szwergold

***

If you need to concatenate a whole bunch of files and a newline between each file’s content—so the concatenation is clean—run an Awk command like this:

    awk 'FNR==1{print ""}1' *.txt > output_files.txt

Just change `*.txt` to match the text file pattern you are attempting to match and change the `output_files.txt` filename as well.

***

*Awk (c) by Jack Szwergold; written on October 8, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*