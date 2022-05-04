# How to Download a YouTube Video (for Davinci Resolve)

(If you have any questions or feedbacks, contact me (@sigh_of_boredom) on Twitter)

**EDIT: As of 2022.5.4, the latest youtube-dl version 2021.12.17 gets throttled by YouTube and suffers from slow download speeds. Even though a [patch](https://github.com/ytdl-org/youtube-dl/pull/30184) to fix it is merged to the master branch (a Git term, don't worry about this), it's still not released. I recommend using [yt-dlp](https://github.com/yt-dlp/yt-dlp) instead (or build (i.e. generate an executable file from source code) the master branch of youtube-dl yourself). yt-dlp mostly replicates the command line interface of youtube-dl so you can use commands/scripts below with yt-dlp as well.**

## Tools

`youtube-dl` is a widely used tool to download YT videos (it actually supports many other websites as well). Though it can handle resolving and fetching video/audio data from YouTube, it requires `ffmpeg` for video/audio processing after downloading, so you should install both.

Both are **command line tools** (which are used through a terminal i.e. Command Prompt or Powershell on Windows), so you want to have a basic idea of how to use a terminal.

If you REALLY don't want to use a terminal, there are some [third-party GUI frontends](https://www.reddit.com/r/youtubedl/wiki/info-guis) of youtube-dl in the wild, but how to use them is out of scope of this article.

## Installing youtube-dl and ffmpeg

### Windows

For youtube-dl, the official repository has a link to an executable.
- https://github.com/ytdl-org/youtube-dl#installation

For ffmpeg, get an executable (not source code) from one of the DL links in the official website.

- https://www.ffmpeg.org/download.html (I recommend to download "ffmpeg-release-full" under "release builds" on https://www.gyan.dev/ffmpeg/builds/)

You have to register ffmpeg on PATH so that it's recognized by youtube-dl. I also highly recommend adding youtube-dl to `PATH` so that you don't have to type their paths every time you use them.

<details>
    <summary>What is PATH?</summary>

TL;DR: It's a list of folder paths. The executables directly under those folders are globally accessible from command line.

Be it Windows, MacOS or Linux, the system has a set of variables called "environment variables". You can create variables with arbitrary names, but some "well-known" variables are used by system programs and/or third-party programs. `PATH` variable is one of them, which contain a list of folder paths. If you add a folder to `PATH`, all the executables (binaries) directly under that folder will be accessible from everywhere by users and programs.

Let's say you downloaded youtube-dl.exe to `C:\Users\<user>\bin\youtube-dl.exe` and you want to use it in Command Prompt. You have to either type out the full absolute path `C:\Users\<user>\bin\youtube-dl.exe` or a relative path from where you are right now. But once you add the folder path `C:\Users\<user>\bin` to the `PATH` variable, you can just type `youtube-dl.exe ...` to use it.
</details>

### Mac and Linux

Chances are you already have some package manager (like apt, Homebrew, etc.) installed, which probably allows you to easily install youtube-dl and ffmpeg ( automatically adding them to PATH). If you don't have any, I recommend you to install one.

## Downloading a video

Downloading a video from YouTube is very simple. You just have to open up a terminal, type the command below and hit enter. This will download the video in the current folder. (Ignore the dollar sign at the beginning, it just means that it's a terminal command.)

```sh
$ youtube-dl -f 'bestvideo[ext=mp4]+bestaudio[ext=m4a]' <video URL OR video ID here>
```

I know this looks intimidating, but there's a reason. Here I'm specifying the video format to be mp4 and audio to m4a because youtube actually offers other formats as well, which Resolve doesn't support (as of the time of writing).

If you want to check what formats YouTube offers for a specific video, run this command.

```
$ youtube-dl -F <video>
```

This will print something like this.

```
$ youtube-dl.exe -F https://www.youtube.com/watch?v=zJr22DkFhBs
[youtube] zJr22DkFhBs: Downloading webpage
[info] Available formats for zJr22DkFhBs:
format code  extension  resolution note
249          webm       audio only tiny   52k , opus @ 50k (48000Hz), 4.02MiB
250          webm       audio only tiny   67k , opus @ 70k (48000Hz), 5.01MiB
140          m4a        audio only tiny  130k , m4a_dash container, mp4a.40.2@128k (44100Hz), 10.60MiB
251          webm       audio only tiny  132k , opus @160k (48000Hz), 9.88MiB
278          webm       256x144    144p  127k , webm container, vp9, 30fps, video only, 7.79MiB
160          mp4        256x144    144p  149k , avc1.4d400c, 30fps, video only, 9.12MiB
242          webm       426x240    240p  277k , vp9, 30fps, video only, 16.70MiB
133          mp4        426x240    240p  333k , avc1.4d4015, 30fps, video only, 20.34MiB
243          webm       640x360    360p  522k , vp9, 30fps, video only, 29.85MiB
134          mp4        640x360    360p  675k , avc1.4d401e, 30fps, video only, 42.91MiB
244          webm       854x480    480p  904k , vp9, 30fps, video only, 53.48MiB
135          mp4        854x480    480p 1231k , avc1.4d401f, 30fps, video only, 78.07MiB
247          webm       1280x718   720p 1753k , vp9, 30fps, video only, 100.14MiB
136          mp4        1280x718   720p 2283k , avc1.64001f, 30fps, video only, 145.09MiB
248          webm       1920x1078  1080p 2832k , vp9, 30fps, video only, 166.56MiB
137          mp4        1920x1078  1080p 4794k , avc1.640028, 30fps, video only, 271.92MiB
18           mp4        640x360    360p  604k , avc1.42001E, 30fps, mp4a.40.2@ 96k (44100Hz), 49.47MiB
22           mp4        1280x718   720p 2197k , avc1.64001F, 30fps, mp4a.40.2@192k (44100Hz) (best)
```

As you can see, YouTube offers three types of streams: audio-only, video-only and video+audio (the last two). And what's weird is that the max quality of the video+audio streams is usually **lower** than those of individual streams (720p vs 1080p in this case). That's why you should download audio and video individually.

You can then use the format codes (the left most column) to manually specify which streams to download like below.

```sh
# DL audio
$ youtube-dl -f 140 zJr22DkFhBs

# DL video and audio separately and merge them into a single file locally (ffmpeg is required for merging)
$ youtube-dl -f 137+140 zJr22DkFhBs
```

The `bestvideo` / `bestaudio` qualifiers in the earlier command tells youtube-dl to automatically choose the best-quality one from the available video-only/audio-only streams.

## Advanced: Downloading a specific portion of a video

Often times, you only need a short part of a video (e.g. 30:00-33:00 of a 1-hour stream). In that case, the above method is inefficient in terms of both time and storage.

Fortunately, you can do that by using both youtube-dl and ffmpeg. I'll first explain how to do it step-by-step, but I'll also give a script to do it all automatically.

### Manual Method

youtube-dl doesn't have a functionality to download only a part of a video. So you first make it just compute the stream urls (without actually downloading) with `-g` option.
```sh
$ youtube-dl -f 'bestvideo[ext=mp4]+bestaudio[ext=m4a]' -g <video>
```
This will print something like this.
```
https://r5---sn-oguelnl7.googlevideo.com/videoplayback? ... &mime=video%2Fmp4& ...
https://r5---sn-oguelnl7.googlevideo.com/videoplayback? ... &mime=audio%2Fmp4& ...
```
As you can see, the first one is an url for the video stream and the second for audio. Then you feed these into ffmpeg as inputs with `-ss` and `-to` options, which specify the time range you want to download. The `-codec copy` option is to make sure ffmpeg doesn't do any transcoding and lose the quality.

```sh
$ ffmpeg -ss <start timestamp> -to <end timestamp> -i <1st URL> -ss <start timestamp> -to <end timestamp> -i <2nd URL> -codec copy '<output filename>.mkv'
```

So for example if you want the part 10:00-11:30, you can run:

```sh
$ ffmpeg -ss 10:00 -to 11:30 -i <1st URL> -ss 10:00 -to 11:30 -i <2nd URL> -codec copy 'bla.mkv'
```

### Automating Using a Shell Script

If you want to make a compilation clip or something, it's too tedious to repeat the above steps for each stream you want to use. In this case, you can automate it using a shell script.

**A nice thing about writing a script in a file like this is reproducibility i.e. you can safely delete the downloaded videos to free up your storage after you're done with the project because if you want to work on it again, you can just run the script again.**

#### Windows

On windows, you can define a powershell function `ytdlrange` like this and use it as many times as you want. I'm wrapping video IDs in quotes because they sometimes start with a hyphen and will be interpreted as an option instead.

```powershell
function ytdlrange {
  param (
    $videoID,
    $start,
    $end,
    $fname
  )

  # mp4 and m4a so that Davinci Resolve can read it
  $urls = youtube-dl -f 'bestvideo[ext=mp4]+bestaudio[ext=m4a]' -g $videoID
  $arr = $urls.Split([Environment]::NewLine)
  ffmpeg -ss $start -to $end -i $arr[0] -ss $start -to $end -i $arr[1] -codec copy -y "$fname.mkv"
}

# usage example
ytdlrange 'edp0gEM9XC0' 0:01 0:03 output1  # save as output1.mkv
ytdlrange '70VjeYKGoHg' 1:30 1:50 output2  # save as output2.mkv
# ... as many as you want
```
Save this as a text file named `dlscript.ps1` (or whatever) in the folder that you want download videos to. Then you run the script with:
```
$ ./dlscript.ps1
```

This will run all the commands in the file line-by-line.

#### Linux / Mac

On Linux and Mac, you can write a Bash script like this. (I haven't tested this so please tell me if it doesn't work)

```sh
function ytdlrange {
  ffmpeg $(youtube-dl -f 'bestvideo[ext=mp4]+bestaudio[ext=m4a]' -g $1 | sed "s/.*/-ss $2 -to $3 -i &/") -codec copy -y $4.mkv
}

# usage example
ytdlrange 'edp0gEM9XC0' 0:01 0:03 output1  # save as output1.mkv
ytdlrange '70VjeYKGoHg' 1:30 1:50 output2  # save as output2.mkv
# ... as many as you want
```

Save above as `dlscript.sh` or something and run it with:
```
$ bash ./dlscript.sh
```
