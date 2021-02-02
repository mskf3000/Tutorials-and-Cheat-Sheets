## FFmpeg

By Jack Szwergold

Extract subtitles from an MP4:

    ffmpeg -i input.mp4 -map 0:s:0 input.srt

Merge subtitles into an MP4 without transcoding:

    ffmpeg -i input.mp4 -i input.srt -c:v copy -c:a copy -c:s mov_text -metadata:s:s:0 language=eng output.mp4

Extract MP4 video out of an MKV file without transcoding:

    ffmpeg -i input.mkv -codec copy output.mp4

Extract MP4 video out of an MKV file without transcoding but specifying a stream using `-map`:

    ffmpeg -i input.mkv -c:v copy -c:a copy -map 0:0 -map 0:1 output.mp4

Same, but strip out all metadata:

    ffmpeg -i input.mkv -map_metadata -1 -c:v copy -c:a copy -map 0:0 -map 0:1 output.mp4

Extract MP4 video out of an MKV file without transcoding video but converting audio to AC3 by specifying a stream using `-map`:

    ffmpeg -i input.mkv -c:v copy -c:a ac3 -map 0:0 -map 0:1 output.mp4

Use this to extract a video — without transcoding — 25 seconds into the video into a new file:

    ffmpeg -ss 25 -i input.mp4 -c:v copy -c:a copy -map 0:0 -map 0:1 output.mp4

For basic h264 encoding, use this command:

	caffeinate ffmpeg -i input.mp4 -vf scale=-1:480 -c:v libx264 -crf 18 -c:a aac -b:a 128k -max_muxing_queue_size 1024 output.mp4

## HEVC (aka: x265)

To encode a video into an HEVC (aka: x265) video that is iOS compatible, you can do this. First, this line lets you create a sample video by using the `-ss` (seek) and `-t` (duration in seconds) parameters. For example, this line will create a 5 minute sample video:

    ffmpeg -i input.mkv -vf scale=-1:720 -ss 0 -t 300 -c:v libx265 -crf 13 -c:a aac -b:a 128k -tag:v hvc1 output.mp4

And then to actually encode that video, you can run this command:

    ffmpeg -i input.mkv -vf scale=-1:720 -c:v libx265 -crf 13 -c:a aac -b:a 128k -tag:v hvc1 output.mp4

Note the `-crf` controls quality: 0 is lossless, 23 is the default and 51 is lossy. Using 13 here for best quality while taking up less space.

***

And example command to create a 10 minute (600 seconds) sample file with the `-ss` and `-t` options:

	caffeinate \
	nice -n 10 \
	ffmpeg -i input.mkv \
	       -ss 0 -t 600 \
	       -map_metadata -1 \
	       -vf scale=-1:720 \
	       -c:v libx265 -crf 20 \
	       -c:a aac -b:a 128k -ac 2 -vol 512 \
	       -tag:v hvc1 -sn output.mp4
	       ;

Same command bit without the `-ss` and `-t` options:

	caffeinate \
	nice -n 10 \
	ffmpeg -i input.mkv \
	       -map_metadata -1 \
	       -vf scale=-1:720 \
	       -c:v libx265 -crf 20 \
	       -c:a aac -b:a 128k -ac 2 -vol 512 \
	       -tag:v hvc1 -sn output.mp4
	       ;

This command uses Apple’s T2 encryption chips (available on most 2018 and above Macs) to speed up encoding. A lot faster than `lbx265` but the quality stinks and the `-crf` value cannot be adjusted. Here for reference only:

	caffeinate \
	nice -n 10 \
	ffmpeg -i input.mkv \
			 -map_metadata -1 \
			 -vf scale=-1:720 \
			 -c:v hevc_videotoolbox -crf 20 \
			 -c:a aac -b:a 128k -ac 2 -vol 512 \
			 -threads 4 -x265-params pools=4 \
			 -tag:v hvc1 -sn \
			 -map 0:0 -map 0:2 output_hevc_videotoolbox.mp4
			 ;

Use this command to fix an MKV with an incorrect duration or other metadata related stuff:

    ffmpeg -err_detect ignore_err -i input.mkv -c copy output.mkv;

***

## Misc. Notes

	ffmpeg -i input.mp4 \
	     -map_metadata -1 \
	     -vf scale=-1:720 \
	     -c:v libx265 -crf 20 \
	     -c:a aac -b:a 128k \
	     -threads 4 \
	     -tag:v hvc1 -sn \
	     -map 0:0 -map 0:1 output.mp4
	     ;

	ffmpeg -i input.mp4 -i input.mkv \
	     -c:v copy \
	     -c:a aac -b:a 128k -ac 2 -af "aresample=matrix_encoding=dplii" \
	     -map 0:0 -map 1:1 \
	     -aspect 4/3 \
	     output_v.mp4
	     ;

	ffmpeg -i input.mp4 -i input.mkv \
	     -c:v copy \
	     -c:a aac -b:a 128k \
	     -aspect 4/3 \
	     -map 0:0 -map 1:1 \
	     output.mp4
	     ;

	ffmpeg -i input.mkv \
	     -c:v copy \
	     -c:a copy \
	     output.mp4
	     ;

	ffmpeg -i input.mkv \
	     -map_metadata -1 \
	     -vf scale=1280:720 -c:v libx265 -crf 20 \
	     -c:a aac -b:a 128k \
	     -threads 4 \
	     -tag:v hvc1 -aspect 4/3 -sn \
	     -map 0:0 -map 0:2 output.mp4
	     ;

***

*FFmpeg (c) by Jack Szwergold; written on November 7, 2018. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
