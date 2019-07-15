## FFmpeg

By Jack Szwergold

Merge subtitles into an MP4 without transcoding:

    ffmpeg -i "input.mp4" -i "input.srt" -c:v copy -c:a copy -c:s mov_text -metadata:s:s:0 language=eng "output.mp4"

Extract MP4 video out of an MKV file without transcoding:

    ffmpeg -i "input.mkv" -codec copy "output.mp4"

***

*FFmpeg (c) by Jack Szwergold; written on November 7, 2018. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
