# Cheat Sheet - RSYNC

By Jack Szwergold, September 17, 2015

#### Command to retain partially transfered files if the connection is lost.

While this works, sometimes the partially transferred files are not cleanly resumed. Should check why that happens and if anything can be done:

    rsync -rPtz /source_directory/ /destination_directory/

- **-r**: Recurse into directories.
- **-z**: Compress file data during the transfer.
- **-t**: Preserve times.
- **-P**: Keep partially transferred files and show transfer progress.

#### Compare two directories with `rsync`.

A nice way to effectively “diff” two directories using `rsync`:

    rsync -rvnc --delete /first_directory_to_check/ /first_directory_to_check/

- **-r**: Recurse into directories,
- **-v**: Increase verbosity.
- **-n**: Dry-run mode; show what would have been transferred.
- **-c**: Checksum the files, not mod-time & size.

***

*Cheat Sheet - RSYNC (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*