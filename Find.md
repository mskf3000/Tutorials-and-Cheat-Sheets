## Find

By Jack Szwergold

### Find files older than a specified span of time.

Find all created (`ctime`) files in the current directory that are older than 14 days:

	find . -maxdepth 1 -type f -ctime +12 -exec ls -la {} \;

Find all modified (`mmin`) files in the current directory that are older than 3 hours (360 minutes):

	find . -maxdepth 1 -type f -mmin +180 -exec ls -la {} \;

Find all modified (`mtime`) files in the current directory that are older than 14 days:

	find . -maxdepth 1 -type f -mtime +14 -exec ls -la {} \;

Find all modified (`mtime`) files in the current directory that are older than 30 days:

	find . -maxdepth 1 -type f -mtime +30 -exec ls -la {} \;

### Find files within a specified span of time.

Find all created (`ctime`) files in the current directory that have been modified within the last 14 days:

	find . -maxdepth 1 -type f -ctime -12 -exec ls -la {} \;

Find all modified (`mmin`) files in the current directory that have been modified within the last 3 hours (360 minutes):

	find . -maxdepth 1 -type f -mmin -180 -exec ls -la {} \;

Find all modified (`mtime`) files in the current directory that have been modified within the last 14 days:

	find . -maxdepth 1 -type f -mtime -14 -exec ls -la {} \;

Find all modified (`mtime`) files in the current directory that have been modified within the last 30 days:

	find . -maxdepth 1 -type f -mtime -30 -exec ls -la {} \;

### Find files based on filesize.

Get a count of all files in the current directory that are larger than 500k:

    find . -type f -size +500k | wc -l

### Find and concatenate a bunch of CSV files.

Find and concatenate CSV files while skipping the first header line of a file:

	find . -name "*.csv" | xargs -n 1 tail -n +2 > ~/Desktop/output.csv

### Create a nicely formatted directory tree.

    find . -print | sed -e 's;[^/]*/;|____;g;s;____|; |;g'

### Mac OS X specific stuff.

#### Finding locked files and unlocking them.

Find locked files in Mac OS X:

    find . -maxdepth 1 -type f -flags uchg

Use this command find locked files in Mac OS X and remove the lock on those found files:

    find . -maxdepth 1 -type f -flags uchg -exec chflags nouchg {} \;

#### Get rid of `.DS_Store` and related cruft files.

Run this command to get rid of `.DS_Store` files:

    find . -name '*.DS_Store' -type f -delete

Run this command to get rid of `.DS_Store`, `.Trashes`, `._*` and  `.TemporaryItems` files:

    find \( -name ".DS_Store" -or -name ".Trashes" -or -name "._*" -or -name ".TemporaryItems" \) -delete

***

*Find (c) by Jack Szwergold; written on September 27, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
