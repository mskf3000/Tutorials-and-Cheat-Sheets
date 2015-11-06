# Zip

By Jack Szwergold, September 11, 2015

### Basic Compression

#### Create a basic Zip archive.

To create an encrypted Zip with full directory paths do this. The `-r` option is for recursing directory paths.

    zip -r [zip file name] [path to directory]

#### Create a basic Zip archive without directory paths.

And to create an encrypted Zip *without* directory paths do this. The `-r` option is for recursing directory paths and the `-j` option is for “junking the paths”; meaning just store the name of a saved file, and do not store directory names:

	zip -jr [zip file name] [path to directory]

***

### Encrypted Compression

#### Create an encrypted Zip archive.

To create an encrypted Zip with full directory paths do this. The `-e` option is for encryption and the `-r` option is for recursing directory paths.

    zip -er [zip file name] [path to directory]

#### Create an encrypted Zip archive without directory paths.

And to create an encrypted Zip *without* directory paths do this. The `-e` option is for encryption, the `-r` option is for recursing directory paths and the `-j` option is for “junking the paths”; meaning just store the name of a saved file, and do not store directory names:

	zip -ejr [zip file name] [path to directory]

***

### Decompression

#### Decompress a Zip archive.

To decompress a Zip archive, just do this:

    unzip [zip file name]

#### Decompress a Zip archive to a specific directory.

And to decompress a Zip archive to a specific directory, just do this. The `-d` option is there to specify an output destination  directory:

    unzip [zip file name] -d [path to destination directory]

#### Decompress multiple Zip files when they have a password.

Use this command to decompress multiple Zip files with a password from the command line:

    unzip -P [password here] \*.zip

***

*Zip (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*