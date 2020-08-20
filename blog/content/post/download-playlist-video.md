---
title: Download playlist video
author: Bill Farmer
type: post
date: 2020-08-18T20:49:05+01:00
url: /download-playlist-video/
categories:
    - hacking
keywords:
    - download
    - playlist
    - video
    - ffmpeg
    - curl
---

Since I wrote [Download streamed video][1] video web sites have upped
their game to stop downloading. One of the techniques used is to use a
[M3U][2] playlist and a complicated set of parameters. Other
techniques are to use JavaScript to create a blob and play that, and
to use a JavaScript browser app which goes into debug mode if you use
the developer tools.

To handle a playlist it is necessary to use the web browser developer
tools. Select the network tab and refresh the relevent web page. Among
the various files downloaded will be a file with a name like
`720p.h264.mp4` or `index.m3u8`. The browser may also start preloading
some of the video segments.

![Url][3]

Copy the url. You may be able to play it with `ffplay`. This will
generate losts of output.

```shell
$ ffplay https://video3.xhcdn.com/key=LJjdx9Px03F-Y2Zmcw4-Kw,end=1597942800,limit=3/\
    data=81.174.214.218/speed=0/initial_buffer=275184/media=hlsA/010/877/489/\
    720p.h264.mp4
```

If so, you can use `ffmpeg` to download the segments and copy them to
a new file. Again, lots of output.

```shell
$ ffmpeg -i https://video3.xhcdn.com/key=+UoaQySV1FrfRWeeVX6dOA,end=1597939200,limit=3/\
    data=81.174.214.218/speed=0/initial_buffer=846704/media=hlsA/013/857/098/\
    720p.h264.mp4 -c copy video.mp4
```

If this doesn't work, use `curl` to download the index file.

```shell
$ curl "https://19-12.b.cdn13.com/hls/bsd/4000/sd/8000/014/953/848/2160p.h264.mp4/index.m3u8?\
    cdn_creation_time=1597773600&cdn_ttl=14400&cdn_cv_data=81.174.214.218&\
    cdn_hash=fa429b99fe263a5cdb34180778526c39" -o index.m3u8
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 10992  100 10992    0     0  53882      0 --:--:-- --:--:-- --:--:-- 53882
```

The file will contain a list of urls for the file segments.

```shell
#EXTM3U
#EXT-X-TARGETDURATION:8
#EXT-X-ALLOW-CACHE:YES
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-VERSION:3
#EXT-X-MEDIA-SEQUENCE:1
#EXTINF:4.000,
https://1-384-19-12.b.cdn13.com/hls/bsd/4000/sd/8000/014/953/848/2160p.h264.mp4/seg-1-v1-a1.ts?\
    cdn_creation_time=1597773600&cdn_ttl=14400&cdn_cv_data=81.174.214.218&\
    cdn_hash=fa429b99fe263a5cdb34180778526c39
#EXTINF:8.000,
https://1-384-19-12.b.cdn13.com/hls/bsd/4000/sd/8000/014/953/848/2160p.h264.mp4/seg-2-v1-a1.ts?\
    cdn_creation_time=1597773600&cdn_ttl=14400&cdn_cv_data=81.174.214.218&\
    cdn_hash=fa429b99fe263a5cdb34180778526c39
```

You can then use curl to download the segments.

```shell
$ curl "https://1-384-19-12.b.cdn13.com/hls/bsd/4000/sd/8000/014/953/848/2160p.h264.mp4/seg-[1-39]-v1-a1.ts?\
    cdn_creation_time=1597773600&cdn_ttl=14400&cdn_cv_data=81.174.214.218&\
    cdn_hash=fa429b99fe263a5cdb34180778526c39" -o "seg-#1-v1-a1.ts"
```

Then use an editor like `emacs` or your favourite text editor to
remove everything except for the file names from the index file.

```shell
#EXTM3U
#EXT-X-TARGETDURATION:8
#EXT-X-ALLOW-CACHE:YES
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-VERSION:3
#EXT-X-MEDIA-SEQUENCE:1
#EXTINF:4.000,
seg-1-v1-a1.ts
#EXTINF:8.000,
seg-2-v1-a1.ts
```

Then use ffmpeg to cancatenate all the segments and turn them into one file.

```shell
$ ffmpeg -i index.m3u8 -c copy video.mp4
```

This will produce a lot of output, and the whole thing is a lot of
hassle, but worth it if you particularly want that video. It should be
possible to automate this with a script using `sed` to edit the index
file and reading through it to find the last segment.

 [1]: download-streamed-video
 [2]: https://en.wikipedia.org/wiki/M3U
 [3]: images/2020/08/Url.png
