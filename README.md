# Apps_5G
Here's a Markdown file summarizing the setup and steps for deploying and testing video streaming using an Apache server, FFmpeg, MPEG-DASH, and DASH.js:

```markdown
# Video Streaming Setup and Testing

## 1. Server Setup

- **Deployed an Apache Server:**
  Set up an Apache server to host video files on a local machine.

  **Install Apache on Ubuntu:**
  ```bash
  sudo apt update
  sudo apt install apache2
  ```

  **Start Apache Service:**
  ```bash
  sudo systemctl start apache2
  ```

  **Enable Apache Service to Start on Boot:**
  ```bash
  sudo systemctl enable apache2
  ```

  **Verify Apache Service Status:**
  ```bash
  sudo systemctl status apache2
  ```

  **Place Video Files in Apache Directory:**
  Copy video files to Apache’s web root directory:
  ```bash
  sudo cp /path/to/testVid.mp4 /var/www/html/
  ```

## 2. Video Preparation

- **Segmented Video Using FFmpeg:**
  Segmented `testVid.mp4` into 4-second chunks at various quality levels to simulate different network conditions.

  **Segment Video:**
  ```bash
  ffmpeg -i testVid.mp4 -codec copy -map 0 -f segment -segment_time 4 -reset_timestamps 1 output_%03d.mp4
  ```

  This command splits the video into 4-second segments, outputting files as `output_000.mp4`, `output_001.mp4`, etc.

## 3. Manifest File Creation

- **Created MPEG-DASH Manifest File (MPD):**
  Created an MPD file that describes the segmented video and metadata required for playback.

  **Example MPD File (`manifest.mpd`):**
  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <MPD xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns="urn:mpeg:dash:schema:mpd:2011"
       xmlns:xlink="http://www.w3.org/1999/xlink"
       xsi:schemaLocation="urn:mpeg:DASH:schema:MPD:2011 http://standards.iso.org/itt>
       profiles="urn:mpeg:dash:profile:isoff-live:2011"
       type="static"
       mediaPresentationDuration="PT35.9S"
       maxSegmentDuration="PT4.0S"
       minBufferTime="PT10.0S">
       <ProgramInformation>
       </ProgramInformation>
       <ServiceDescription id="0">
       </ServiceDescription>
       <Period id="0" start="PT0.0S">
           <AdaptationSet id="0" contentType="video" startWithSAP="1" segmentAlignment="true">
               <Representation id="0" mimeType="video/mp4" codecs="avc1.640028" width="1280" height="720">
                   <SegmentTemplate timescale="12800" initialization="init.mp4">
                       <SegmentTimeline>
                           <S t="0" d="64000" r="2" />
                           <S d="62976" />
                           <S d="64000" r="2" />
                           <S d="12800" />
                       </SegmentTimeline>
                   </SegmentTemplate>
               </Representation>
           </AdaptationSet>
           <AdaptationSet id="1" contentType="audio" startWithSAP="1" segmentAlignment="true">
           </AdaptationSet>
       </Period>
  </MPD>
  ```

  Place this manifest file in the Apache server’s web root directory.

## 4. Client Setup

- **Used DASH.js to Simulate Playback:**
  Configured DASH.js to fetch and play the segmented video chunks based on the DASH protocol.

  **Include DASH.js in HTML:**
  ```html
  <!DOCTYPE html>
  <html>
  <head>
      <title>DASH.js Video Playback</title>
      <script src="https://cdn.dashjs.org/latest/dash.all.min.js"></script>
  </head>
  <body>
      <video id="videoPlayer" controls></video>
      <script>
          var url = 'http://<Server-IP>/manifest.mpd'; // Replace <Server-IP> with your server's IP address
          var player = dashjs.MediaPlayer().create();
          player.initialize(document.querySelector("#videoPlayer"), url, true);
      </script>
  </body>
  </html>
  ```

  **Access Video on Local Network:**
  Ensure both your server and client (phone) are connected to the same network and access the URL in your phone’s browser.

```

**Summary:**

1. Set up and configured Apache server.
2. Used FFmpeg to segment the video into chunks.
3. Created an MPEG-DASH manifest file.
4. Used DASH.js to play the video and tested access on a local network.

This Markdown file includes all commands and steps needed to set up the server, prepare video files, create a manifest, and set up a client for video playback.
