## Find - Audio Processing Items

By Jack Szwergold

### Convert FLAC to CBR MP3 files.

Convert FLAC audio files into MP3 audio files at 320k CBR:

    find -E "Desktop/Audio" -type f -iregex ".*\.(FLAC)$" |\
      while read full_audio_filepath
      do
        ffmpeg -y -v quiet -nostdin -i "$full_audio_filepath" -ab 320k -ac 2 -map_metadata 0 -id3v2_version 3 "`sed 's/flac/mp3/g' <<< ${full_audio_filepath}`";
      done

### Convert CBR MP3 to VBR MP3 files.

Convert MP3 audio files with LAME into VBR files:

    find -E "Desktop/Audio" -type f -iregex ".*\.(MP3)$" |\
      while read full_audio_filepath
      do
        lame --quiet -m s --lowpass 19.7 -V 3 --vbr-new -q 0 -b 96 --scale 0.99 --athaa-sensitivity 1 "$full_audio_filepath" "`sed 's/\.mp3/\ LAME.mp3/g' <<< ${full_audio_filepath}`";
      done

### Convert FLAC to VBR MP3 files.

Convert FLAC audio files into MP3 audio by piping them through LAME for VBR output:

    find -E "Desktop/Audio" -type f -iregex ".*\.(FLAC)$" |\
      while read full_audio_filepath
      do
        ffmpeg -y -v quiet -nostdin -i "${full_audio_filepath}" -ab 320k -ac 2 -f s16le -acodec pcm_s16le - | \
          lame --quiet -r -m s --lowpass 19.7 -V 3 --vbr-new -q 0 -b 96 --scale 0.99 --athaa-sensitivity 1 - "`sed 's/flac/mp3/g' <<< ${full_audio_filepath}`";
      done


### Convert FLAC to VBR MP3 files in an MP3 subdirectory.

Convert FLAC audio files into MP3 audio by piping them through LAME for VBR output. The output gets placed in an `mp3` subdirectory:

Convert FLAC audio files into MP3 audio by piping them through LAME for VBR output:

    find -E "Desktop/Audio" -type f -iregex ".*\.(FLAC)$" |\
      while read full_audio_filepath
      do

        # Break up the full audio filepath stuff into different directory and filename components.
        audio_dirname=$(dirname "${full_audio_filepath}");
        audio_basename=$(basename "${full_audio_filepath}");
        audio_filename="${audio_basename%.*}";
        # audio_extension="${audio_basename##*.}";

        # Set the MP3
        mp3_dirpath="${audio_dirname}/mp3";
        mp3_filepath_temp="${mp3_dirpath}/${audio_filename}.mp3.TMP";
        mp3_filepath="${mp3_dirpath}/${audio_filename}.mp3";
        metadata_filepath="${mp3_dirpath}/${audio_filename}.txt.TMP";

        # Set the metadata to a file.
        # ffmpeg -y -nostdin -i "${full_audio_filepath}" -f ffmetadata "${metadata_filepath}";

        # Create the child MP3 directory.
        mkdir -p "${mp3_dirpath}";

        # Where the magic happens.
        ffmpeg -y -v quiet -nostdin -i "${full_audio_filepath}" -f ffmetadata "${metadata_filepath}" -ab 320k -ac 2 -f s16le -acodec pcm_s16le - | \
          lame --quiet -r -m s --lowpass 19.7 -V 3 --vbr-new -q 0 -b 96 --scale 0.99 --athaa-sensitivity 1 - "${mp3_filepath_temp}";

        # Now copy the metadata back into the MP3 file.
        # TODO: Sloppy process; should not have to copy the same file again to embed the metadata.
        ffmpeg -y -v quiet -nostdin -i "${mp3_filepath_temp}" -i "${metadata_filepath}" -map_metadata 1 -c:a copy -id3v2_version 3 -write_id3v1 1 "${mp3_filepath}";

      done

***

*Find - Audio Processing Items (c) by Jack Szwergold; written on January 1, 2017. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*