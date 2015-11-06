# Diff

By Jack Szwergold, September 15, 2015

***

#### Diff’ing directory content.

This is useful if a website has been hacked and you want to compare a clean archive of the content to the website data in it’s current state to track down what files have been affected and infected.

Compare two different directories and show the filenames and content differences:

    diff -Naur /path/of/stuff/to/compare/1 /path/of/stuff/to/compare/2
    
Quickly compare two different directories and show only the filenames of items that are different:

    diff -qr /path/of/stuff/to/compare/1 /path/of/stuff/to/compare/2

Use this to compare two different directories and send the output to a sorted text file named `diffs.txt`: 

    diff -qr /path/of/stuff/to/compare/1 /path/of/stuff/to/compare/2 | grep -v -e 'DS_Store' -e 'Thumbs' | > sort > stuff_compared.txt

***

*Diff (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*