## Find - Text Processing Items

By Jack Szwergold

This simple script uses `tr`:

      tr -d '\r' < "${full_text_filepath}" > "${full_text_filepath}".txt;

### Convert old Mac OS line endings to modern text files using `dos2unix`.

And here you go:

  find -E "Desktop/Text" -type f |\
    while read full_text_filepath
    do
      dos2unix -k -n -c mac "${full_text_filepath}" "${full_text_filepath}".txt;
    done

***

*Find - Text Processing Items (c) by Jack Szwergold; written on May 19, 2021. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
