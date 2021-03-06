---
date: 2018-08-22T00:00:00Z
title: Process MKV files
tags:
  - Video
---

Here is a list of things you might want to do once you get your hands on a MKV
file.

<!--more-->

## Transcode the soundtrack to some other format

    for x in *.mkv
        ffmpeg -i $x -c:v copy -c:a aac $x.mp4
    end

## Extract a subtitle track

    mkvmerge -i file.mkv
    mkvextract tracks file.mkv 1:file-en.srt

## VOBSUB to SRT

Given you just extracted a `file.idx` and `file.sub` file pair and have
`vobsub2srt` installed

    vobsub2srt file

NB: For OCR to work, a per language package must be installed. For instance,
for English:

    pacman -S tesseract-data-eng

If your player only supports a limited set of encodings or there is a mismatch
of encodings somewhere and you are satisfied with ASCII only output try:

    iconv -f utf8 -t ascii//translit < before.srt > after.srt
