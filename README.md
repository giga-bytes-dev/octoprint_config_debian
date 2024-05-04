# octoprint_config_debian

1) config wifi auto connect TODO add

```
sudo apt install python3.11-venv
sudo apt-get install python3-dev
sudo apt-get install build-essential

python3 -m venv octoprintvenv
source octoprintvenv/bin/activate
pip3 install OctoPrint

```

add as service octoprint system
camera-streamer add
/etc/systemd/system/camera-streamer.service

actoprint1.service
```
[Unit]
Description=Octoprint
After=syslog.target
After=network.target
[Install]
WantedBy=multi-user.target
[Service]
Type=simple
User=printer
Group=printer
WorkingDirectory=/home/printer
ExecStart=/home/printer/octoprintvenv/bin/python3 /home/printer/octoprintvenv/bin/octoprint serve --host=0.0.0.0 --port=8080
Environment="PATH=/home/printer/octoprintvenv/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
StandardOutput=syslog
StandardError=syslog
```

camera-streamer.service
```
[Unit]
Description=camera-streamer web camera for USB camera on Generic platform
After=network.target
ConditionPathExistsGlob=/dev/v4l/by-id/usb-*-video-index0

[Service]
ExecStart=/usr/bin/camera-streamer \
  -camera-path=/dev/video0 \
  -camera-format=MJPG \
  -camera-width=1920 -camera-height=1080 \
  ;-camera-width=1080 -camera-height=760
  -camera-fps=25 \
  ; use two memory buffers to optimise usage
  -camera-nbufs=3 \
  --http-listen=0.0.0.0 \
  --http-port=8090 \
  ; disable video streaming (WebRTC, RTSP, H264)
  ; on non-supported platforms
  -camera-video.disabled

#DynamicUser=yes
User=printer
Group=printer
SupplementaryGroups=video
Restart=always
RestartSec=10
Nice=10
IOSchedulingClass=idle
IOSchedulingPriority=7
CPUWeight=60
AllowedCPUs=1-2
MemoryMax=500M

[Install]
WantedBy=multi-user.target
```
