## FFmpeg

By Jack Szwergold

Extract subtitles from an MP4:

    ffmpeg -i input.m4v -map 0:s:0 input.srt

Merge subtitles into an MP4 without transcoding:

    ffmpeg -i input.m4v -i input.srt -c:v copy -c:a copy -c:s mov_text -metadata:s:s:0 language=eng output.m4v

Extract MP4 video out of an MKV file without transcoding:

    ffmpeg -i input.mkv -codec copy output.m4v

Extract MP4 video out of an MKV file without transcoding but specifying a stream using `-map`:

    ffmpeg -i input.mkv -c:v copy -c:a copy -map 0:0 -map 0:1 output.m4v

Same, but strip out all metadata:

    ffmpeg -i input.mkv -map_metadata -1 -c:v copy -c:a copy -map 0:0 -map 0:1 output.mp4

Extract MP4 video out of an MKV file without transcoding video but converting audio to AC3 by specifying a stream using `-map`:

    ffmpeg -i input.mkv -c:v copy -c:a ac3 -map 0:0 -map 0:1 output.m4v

## HEVC (aka: x265)

To encode a video into an HEVC (aka: x265) video that is iOS compatible, you can do this. First, this line lets you create a sample video by using the `-ss` (seek) and `-t` (duration in seconds) parameters. For example, this line will create a 5 minute sample video:

    ffmpeg -i input.mkv -vf scale=-1:720 -ss 0 -t 300 -c:v libx265 -crf 13 -c:a aac -b:a 128k -tag:v hvc1 output.mp4

And then to actually encode that video, you can run this command:

    ffmpeg -i input.mkv -vf scale=-1:720 -c:v libx265 -crf 13 -c:a aac -b:a 128k -tag:v hvc1 output.mp4

Note the `-crf` controls quality: 0 is lossless, 23 is the default and 51 is lossy. Using 13 here for best quality while taking up less space. 

***

*FFmpeg (c) by Jack Szwergold; written on November 7, 2018. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
