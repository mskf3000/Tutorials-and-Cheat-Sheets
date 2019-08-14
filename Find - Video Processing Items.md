## Find - Video Processing Items

By Jack Szwergold

### Video Metadata Parser

This script (`video_metadata_parser.sh`) uses ExifTool to parse dates, times and titles from a directory of video files on the desktop. Info is saved on the desktop into a dated text file named something like `VideoInfo-2017-07-25.txt`:

	find -E "Desktop/Videos" -type f -iregex ".*\.(M4V|MOV)$" |\
	  while read full_video_filepath
	  do
	
	    # Break up the full audio filepath stuff into different directory and filename components.
	    video_dirname=$(dirname "${full_video_filepath}");
	    video_basename=$(basename "${full_video_filepath}");
	    video_filename="${video_basename%.*}";
	    video_extension="${video_basename##*.}";
	
	    # Get the video metadata.
	    video_date=$(exiftool -s -s -s -CreationDate "${full_video_filepath}");
	    video_title=$(exiftool -s -s -s -Title "${full_video_filepath}");
	
	    # Reformat the file creation date to be Flickr friendly.
	    if [ ! -z "${video_date}" ]; then
	      video_date_formatted=$(date -jf"%Y:%m:%d %k:%M:%S" "${video_date%-*}" +"%m/%d/%Y %k:%M:%S");
	    else
	      video_date_formatted="";
	    fi
	
	    # Reformat the file creation date to be Flickr friendly.
	    echo "${video_filename} | ${video_date_formatted} | ${video_title}" >> "Desktop/VideoInfo-$(date +%Y-%m-%d).txt";
	
	  done

### Extract MP4 Videos Out of an MKV File

Simple script traverse a directory filled with MKV files and extract the MP4 inside without transcoding:

	find -E 'Desktop/Movies' -type f -iregex '.*\.(MKV)$' |\
	  while read FULL_PATH
	  do
	    PATH_SANS_EXTENSION="${FULL_PATH%.*}"
	    ffmpeg -i "${FULL_PATH}" -codec copy "${PATH_SANS_EXTENSION}".mp4
	  done

***

*Find - Video Processing Items (c) by Jack Szwergold; written on July 25, 2017. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*