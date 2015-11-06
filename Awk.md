# Cheat Sheet - Awk

By Jack Szwergold, October 8, 2015

If you need to concatenate a whole bunch of files and a newline between each file’s content—so the concatenation is clean—run an Awk command like this:

    awk 'FNR==1{print ""}1' *.txt > output_files.txt

Just change `*.txt` to match the text file pattern you are attempting to match and change the `output_files.txt` filename as well.

***

*Cheat Sheet - Awk (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*