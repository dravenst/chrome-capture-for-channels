## chrome-capture-for-channels

Capture video and audio from a Chrome tab using the [`chrome.tabCapture`](https://developer.chrome.com/docs/extensions/reference/tabCapture/) API. Built on [Puppeteer](https://pptr.dev/) and [puppeteer-stream](https://github.com/SamuelScheit/puppeteer-stream)

### setup

See detailed installation/configuration instructions here:
[Chrome Capture Thread](https://community.getchannels.com/t/chrome-capture-for-channels/36667/130)

Download the latest [release](https://github.com/dravenst/chrome-capture-for-channels/releases) for Windows

### usage

An http server is listening on port 5589 and responds to these routes. the response is a webm stream with h264 video and opus audio.

- `/stream/<name>` for stream names registered in the code
- `/stream?url=<url>` for other arbitrary URLs

URL support includes nbc.com, slingTV, peacocktv.com [(how to do peacock links)](https://community.getchannels.com/t/adbtuner-a-channel-tuning-application-for-networked-google-tv-android-tv-devices/36822/1895), Google Photo album slideshow, Spectrum, and early testing support for Directv.  [Here](https://github.com/dravenst/chrome-capture-for-channels/blob/main/sample-m3u-sling-channel-definitions.txt) are some more samples for your Channels M3U file (HLS) setup.

Setup a new Custom Channel using:

```
#EXTM3U
#EXTINF:-1 channel-id="weatherscan",Weatherscan
chrome://x.x.x.x:5589/stream?url=https://weatherscan.net

#EXTINF:-1 channel-id="Bravo (East)",Bravo (East)
chrome://x.x.x.x:5589/stream/bravo

#EXTINF:-1 channel-id="CC" channel-number="107" tvc-guide-stationid="62420", Comedy Central
chrome://x.x.x.x:5589/stream?url=https://watch.sling.com/1/channel/29938328f60d447299ec48511a09ebab/watch

#EXTINF:-1 channel-id="golf" channel-number="401" tvc-guide-stationid="61854", GOLFHD
chrome://x.x.x.x:5589/stream?url=https://watch.sling.com/1/channel/e7c98734f996492187bb868ce5655a0e/watch

#EXTINF:-1 channel-id="Hallmark",Hallmark
chrome://x.x.x.x:5589/stream/?url=https://www.peacocktv.com/deeplink?deeplinkData=%7B%22serviceKey%22%3A%224846937553519166117%22%2C%22type%22%3A%22LINEAR_CHANNEL%22%2C%22action%22%3A%22PLAY%22%7D

#EXTINF:-1 channel-id=Google Album",Google Album
chrome://x.x.x.x:5589/stream?url=https://photos.app.goo.gl/(rest of link from a shared album here)

#EXTINF:-1 channel-id="ESPN",ESPN
chrome://x.x.x.x:5589/stream?url=http://stream.directv.com/guide/&ch=1234


```

### windows service install

Download the executable file from the latest [release](https://github.com/dravenst/chrome-capture-for-channels/releases) (i.e. `chrome-capture-for-channels-win-x64.exe`)

Create a new text file called `channels-win-x64.ps1` and add the following line to it (replacing `(YOUR-PATH)` with your path to where you stored the .exe file):
`Start-Process -WindowStyle hidden "(YOUR-PATH)\chrome-capture-for-channels-win-x64.exe"`

For Windows, create a new task to run the `channels-win-x64.ps1` file above in Windows Task Scheduler with the highest privileges, and set it to trigger it when the user logs on (it's critical to run after user login to enable the GPU).

Run the new Windows task you created manually to test it and be sure to visit all of the streaming sites within the browser that pops up after you try to stream your first channel.  This will allow you to login to the sites and retain the credentials for later runs. (Note: the browser data directory is created in a different location so any previous browser credentials won't be available.)

Other notes: 

- Add additional startup parameters (listed below) as necessary to the `channels-win-x64.ps1` file (e.g. change the default video codec if you don't have an Nvidia GPU)
- The browser will attempt to run the video in full screen mode.  It is important not to run any other browsers/apps in full screen while this is running as it will significantly impact streaming performance.

### development

To setup a development environment where you can edit and run `main.js`, follow the appropriate OS environment installation and setup instructions below and then run `node main.js` with the options listed below.

```
Usage: node main.js [options]

Options:
  -v, --videoBitrate  Video bitrate in bits per second  [number] [default: 6000000]
  -a, --audioBitrate  Audio bitrate in bits per second  [number] [default: 256000]
  -f, --frameRate     Minimum frame rate  [number] [default: 30]
  -p, --port          Port number for the server  [number] [default: 5589]
  -w, --width         Video width in pixels (e.g., 1920 for 1080p)  [number] [default: 1920]
  -h, --height        Video height in pixels (e.g., 1080 for 1080p)  [number] [default: 1080]
  -i, --videoCodec    Video codec (e.g., h264_nvenc, h264_qsv, h264_amf, h264_vaapi)  [string] [default: "h264_nvenc"]
  -u, --audioCodec    Audio codec (e.g., aac, opus)  [string] [default: "aac"]
  -?, --help          Show help  [boolean]

Examples:
  node main.js -v 6000000 -a 192000 -f 30 -w 1920 -h 1080
  node main.js --videoBitrate 8000000 --frameRate 60 --width 1920 --height 1080
```

#### windows

```
winget install -e --id Git.Git
winget install -e --id OpenJS.NodeJS

git clone https://github.com/dravenst/chrome-capture-for-channels
cd chrome-capture-for-channels
npm install
node main.js
```

#### mac

```
brew install nodejs git

git clone https://github.com/dravenst/chrome-capture-for-channels
cd chrome-capture-for-channels
npm install
node main.js
```

#### build an executable

```
npm run build
```
(For building on Windows, open package.json, look under "targets", and remove everything besides latest-win-x64.)
