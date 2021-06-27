## Find - Audio Processing Items

By Jack Szwergold

### Convert FLAC to CBR MP3 files.

Convert FLAC audio files into MP3 audio files at 320k CBR:

    find -E "Desktop/Audio" -type f -iregex ".*\.(FLAC)$" |\
      while read full_audio_filepath
      do
        ffmpeg -y -v quiet -nostdin -i "$full_audio_filepath" -ar 44100 -sample_fmt s16 -ac 2 -map_metadata 0 -id3v2_version 3 "`sed 's/flac/mp3/g' <<< ${full_audio_filepath}`";
      done

### Convert CBR MP3 to VBR MP3 files.

Convert AIFF audio files with LAME into VBR MP3 files:

    find -E "Desktop/Audio" -type f -iregex ".*\.(MP3)$" |\
      while read full_audio_filepath
      do
        lame --quiet -m s --lowpass 19.7 -V 3 --vbr-new -q 0 -b 96 --scale 0.99 --athaa-sensitivity 1 "$full_audio_filepath" "`sed 's/\.mp3/\ LAME.mp3/g' <<< ${full_audio_filepath}`";
      done

### Trim 1/8th of a second off of the beginning of an MP3 file.

Strip an 1/8 of a second off of the beginning of an MP3 file; useful for times when headers accidentally get encoded and sound like a loud pop right at the beginning of an MP3:

    find -E "Desktop/Audio" -type f -iregex ".*\.(MP3)$" |\
      while read full_audio_filepath
      do

	     # Break up the full audio filepath stuff into different directory and filename components.
	     audio_dirname=$(dirname "${full_audio_filepath}");
	     audio_basename=$(basename "${full_audio_filepath}");
	     audio_filename="${audio_basename%.*}";
	     # audio_extension="${audio_basename##*.}";
	
	     # Set the MP3 directory.
	     mp3_dirpath="${audio_dirname}/mp3";
	     mp3_filepath="${mp3_dirpath}/${audio_filename}.mp3";
	
	     # Create the child MP3 directory.
	     mkdir -p "${mp3_dirpath}";

	     # And here is where the magic happens.
        ffmpeg -y -v quiet -ss 0.125 -i "$full_audio_filepath" -acodec copy "$mp3_filepath" < /dev/null;

      done

### Trim seconds off of the end of an MP3 file.

Strip seconds off of the end of an MP3 file. In this case 6.875 seconds:

    find -E "Desktop/Audio" -type f -iregex ".*\.(MP3)$" |\
      while read full_audio_filepath
      do

	     # Break up the full audio filepath stuff into different directory and filename components.
	     audio_dirname=$(dirname "${full_audio_filepath}");
	     audio_basename=$(basename "${full_audio_filepath}");
	     audio_filename="${audio_basename%.*}";
	     # audio_extension="${audio_basename##*.}";
	
	     # Set the MP3 directory.
	     mp3_dirpath="${audio_dirname}/mp3";
	     mp3_filepath="${mp3_dirpath}/${audio_filename}.mp3";
	
	     # Create the child MP3 directory.
	     mkdir -p "${mp3_dirpath}";

	     # And here is where the magic happens.
	     mp3_duration=$(ffprobe -v error -select_streams a:0 -show_entries stream=duration -of default=noprint_wrappers=1:nokey=1 "$full_audio_filepath" < /dev/null);
	     trimmed_duration=$(echo $mp3_duration - 6.875 | bc);
	     # echo $trimmed_duration;
	     ffmpeg -y -v quiet -ss 0 -to "$trimmed_duration" -i "$full_audio_filepath" -acodec copy "$mp3_filepath" < /dev/null;

      done

### Convert AIFF files to VBR MP3 files.

Convert MP3 audio files with LAME into VBR files:

    find -E "Desktop/Audio" -type f -iregex ".*\.(AIFF)$" |\
      while read full_audio_filepath
      do
        lame --quiet -m s --lowpass 19.7 -V 3 --vbr-new -q 0 -b 96 --scale 0.99 --athaa-sensitivity 1 "$full_audio_filepath" "`sed 's/\.aiff/\.mp3/g' <<< ${full_audio_filepath}`";
      done

### Convert FLAC to VBR MP3 files.

Convert FLAC audio files into MP3 audio by piping them through LAME for VBR output:

    find -E "Desktop/Audio" -type f -iregex ".*\.(FLAC)$" |\
      while read full_audio_filepath
      do
        ffmpeg -y -v quiet -nostdin -i "${full_audio_filepath}" -ar 44100 -sample_fmt s16 -ac 2 -f s16le -acodec pcm_s16le - | \
          lame --quiet -r -m s --lowpass 19.7 -V 3 --vbr-new -q 0 -b 96 --scale 0.99 --athaa-sensitivity 1 - "`sed 's/flac/mp3/g' <<< ${full_audio_filepath}`";
      done

### Convert FLAC and M4A files to VBR MP3 files in an MP3 subdirectory.

Convert FLAC audio files into MP3 audio by piping them through LAME for VBR output. The output gets placed in an `mp3` subdirectory:

	find -E "Desktop/Audio" -type f -iregex ".*\.(FLAC|M4A|WMA|APE|AIFF)$" |\
	  while read full_audio_filepath
	  do
	
	    # Break up the full audio filepath stuff into different directory and filename components.
	    audio_dirname=$(dirname "${full_audio_filepath}");
	    audio_basename=$(basename "${full_audio_filepath}");
	    audio_filename="${audio_basename%.*}";
	    # audio_extension="${audio_basename##*.}";
	
	    # Set the MP3 directory.
	    mp3_dirpath="${audio_dirname}/mp3";
	    mp3_filepath="${mp3_dirpath}/${audio_filename}.mp3";
	
	    # Create the child MP3 directory.
	    mkdir -p "${mp3_dirpath}";
	
	    # Get the track metadata.
	    mp3_title=$(ffprobe 2> /dev/null -show_format "${full_audio_filepath}" | grep -i TAG:TITLE= | cut -d '=' -f 2- );
	    mp3_artist=$(ffprobe 2> /dev/null -show_format "${full_audio_filepath}" | grep -i TAG:ARTIST= | cut -d '=' -f 2- );
	    mp3_album=$(ffprobe 2> /dev/null -show_format "${full_audio_filepath}" | grep -i TAG:ALBUM= | cut -d '=' -f 2- );
	    mp3_year=$(ffprobe 2> /dev/null -show_format "${full_audio_filepath}" | grep -i TAG:YEAR= | cut -d '=' -f 2- );
	    mp3_track=$(ffprobe 2> /dev/null -show_format "${full_audio_filepath}" | grep -i TAG:TRACK= | cut -d '=' -f 2- | sed 's/^0*//' );
	    mp3_tracktotal=$(ffprobe 2> /dev/null -show_format "${full_audio_filepath}" | grep -i TAG:TRACKTOTAL= | cut -d '=' -f 2- | sed 's/^0*//' );
	    mp3_genre=$(ffprobe 2> /dev/null -show_format "${full_audio_filepath}" | grep -i TAG:GENRE= | cut -d '=' -f 2- );
	
	    # Where the magic happens.
	    ffmpeg -y -v quiet -nostdin -i "${full_audio_filepath}" -ar 44100 -sample_fmt s16 -ac 2 -f s16le -acodec pcm_s16le - | \
	      lame --quiet --add-id3v2 --pad-id3v2 --tt "${mp3_title}" --ta "${mp3_artist}" --tl "${mp3_album}" --tn "${mp3_track}"/"${mp3_tracktotal}" --tg "${mp3_genre}" -r -m s --lowpass 19.7 -V 3 --vbr-new -q 0 -b 96 --scale 0.99 --athaa-sensitivity 1 - "${mp3_filepath}";
	
	  done

***

*Find - Audio Processing Items (c) by Jack Szwergold; written on January 1, 2017. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*