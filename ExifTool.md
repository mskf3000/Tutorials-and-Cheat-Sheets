## ExifTool

By Jack Szwergold

### Some ExifTool example commands.

Shows all EXIF info embedded in the file:

    exiftool [filename]

Shows all EXIF info embedded in the file including schema info:

    exiftool -a -G1 -s [filename]

Shows EXIF info stored in the “File” schema:L

    exiftool -a -File:all [filename]

Shows EXIF info stored in the XMP Dublin Core schema:

    exiftool -a -XMP-dc:all [filename]

Remove all EXIF data from a file:

    exiftool -all= [filename]

Remove all data connected to the XMP Dublin Core schema:

    exiftool -a -XMP-dc:all= [filename]

### Options available for ExifTool.

	-TAG or --TAG                    Extract or exclude specified tag 
	-TAG[+-]=[VALUE]                 Write new value for tag 
	-TAG[+-]<=DATFILE                Write tag value from contents of file 
	-TAG[+-]<SRCTAG                  Copy tag value (see -tagsFromFile) 
	
	-@ ARGFILE                       Read command-line arguments from file 
	-a          (-duplicates)        Allow duplicate tag names in output 
	-b          (-binary)            Output data in binary format 
	-c FMT      (-coordFormat)       Set format for GPS coordinates 
	-charset [[TYPE=]CHARSET]        Specify encoding for special characters 
	-d FMT      (-dateFormat)        Set format for date/time values 
	-D          (-decimal)           Show tag ID numbers in decimal 
	-e          (--composite)        Do not calculate composite tags 
	-E, -ex     (-escape(HTML|XML))  Escape values for HTML (-E) or XML (-ex) 
	-ee         (-extractEmbedded)   Extract information from embedded files 
	-ext EXT    (-extension)         Process files with specified extension 
	-f          (-forcePrint)        Force printing of all specified tags 
	-F[OFFSET]  (-fixBase)           Fix the base for maker notes offsets 
	-fast[NUM]                       Increase speed for slow devices 
	-fileOrder [-]TAG                Set file processing order 
	-g[NUM...]  (-groupHeadings)     Organize output by tag group 
	-G[NUM...]  (-groupNames)        Print group name for each tag 
	-geotag TRKFILE                  Geotag images from specified GPS log 
	-h          (-htmlFormat)        Use HMTL formatting for output 
	-H          (-hex)               Show tag ID number in hexadecimal 
	-htmlDump[OFFSET]                Generate HTML-format binary dump 
	-i DIR      (-ignore)            Ignore specified directory name 
	-if EXPR                         Conditionally process files 
	-j          (-json)              Use JSON output format 
	-k          (-pause)             Pause before terminating 
	-l          (-long)              Use long 2-line output format 
	-L          (-latin)             Use Windows Latin1 encoding 
	-lang [LANG]                     Set current language 
	-list[w|f|wf|g[NUM]|d|x]         List various exiftool attributes 
	-m          (-ignoreMinorErrors) Ignore minor errors and warnings 
	-n          (--printConv)        Disable print conversion 
	-o OUTFILE  (-out)               Set output file or directory name 
	-overwrite_original              Overwrite original by renaming tmp file 
	-overwrite_original_in_place     Overwrite original by copying tmp file 
	-p FMTFILE  (-printFormat)       Print output in specified format 
	-P          (-preserve)          Preserve date/time of original file 
	-q          (-quiet)             Quiet processing 
	-r          (-recurse)           Recursively process subdirectories 
	-s          (-short)             Short output format 
	-S          (-veryShort)         Very short output format 
	-scanForXMP                      Brute force XMP scan 
	-sep STR    (-separator)         Set separator string for list items 
	-struct                          Use structured XML or JSON output 
	-t          (-tab)               Output in tab-delimited list format 
	-T          (-table)             Output in tabular format 
	-tagsFromFile SRCFILE            Copy tag values from file 
	-u          (-unknown)           Extract unknown tags 
	-U          (-unknown2)          Extract unknown binary tags too 
	-use MODULE                      Add features from plug-in module 
	-v[NUM]     (-verbose)           Print verbose messages 
	-ver                             Print exiftool version number 
	-w EXT      (-textOut)           Write output text files 
	-x TAG      (-exclude)           Exclude specified tag 
	-X          (-xmlFormat)         Use RDF/XML output format 
	-z          (-zip)               Read/write compressed information 
	
	-common_args                     Define common arguments 
	-config CFGFILE                  Specify configuration file name 
	-execute                         Execute multiple commands on one line 
	-srcfile FMT                     Set different source file name


### Uninstalling ExifTool on macOS

Just run this command to uninstall ExifTool and the related `lib/` directory from macOS; both are located in the `/usr/local/bin/` directory:

    rm -rf /usr/local/bin/{exiftool,lib}

***

*ExifTool (c) by Jack Szwergold; written on September 14, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*