<h1 align="center">AIBOX DISTRIBUTED REID APPLICATION</h1>

## Introduction
This repository contains source code of AIBox Distributed ReID application. 
The AIBox Distribution accelerated application performs distributed, scalable, multi-stream cross tracking and Re-Identification. The application leverages machine learning for pedestrian tracking and decoding multiple camera streams and performs pedestrian detection and tracking across camera feeds. Common applications include smart cities, retail analytics, and video analytics.

<div align="center">
  <img src="./media/aibox-dist-landing.png" width=700>
</div>

For detailed documentation of this application please visit the [documentation page](https://xilinx.github.io/kria-apps-docs/kv260/2022.1/build/html/docs/aibox/aibox-dist_landing.html)

## Development Guide

If you want to cross compile the source in Linux PC machine, follow these steps, otherwise skip this section.

1. Refer to the `K260 SOM Starter Kit Tutorial` to build the cross-compilation SDK, and install it to the path you choose or default. Suppose it's SDKPATH.

2. Run "./build.sh ${SDKPATH}" in the source code folder of current application, to build the application. <a name="build-app"></a>

3. The build process in [2](#build-app) will produce a rpm package aibox-dist-1.0.1-1.aarch64.rpm under build/, upload to the board, and run "rpm -ivh --force ./aibox-dist-1.0.1-1.aarch64.rpm" to update install.

## Setting up the Board and Application Deployment
A step by step tutorial and details on how to setup the board and run this application is given in the [AIBox Distributed Reid Documentation](https://xilinx.github.io/kria-apps-docs/kv260/2022.1/build/html/docs/aibox/docs/app_deployment_aib.html#). Please visit the documentation page for more details.


### **How to Run The Application**

This application consists of up to 4 server side image capturing boards with camera, and 1 client side monitoring board. So accordingly there are different binary executable to be run on **server** or **client**.

### Start The Server Application

**Note** The application needs to be run with ***sudo***.

#### Examples

  This example starts capturing by using the MIPI camera as input source.

  ```bash
  sudo aibox-dist-cam -m 
  ```

  You can also start the streaming by using video files as the input source, actually, this is the recommended mode to start with.

  ```bash
  sudo aibox-dist-cam -f cros.1.1080.h264
  ```

**Note**: Only one instance of aibox-dist-cam application can run at a time because it requires exclusive access to a DPU engine and there is only one instance of DPU that exists in the aibox-cam platform.

##### Command Options

The example shows a simple way to invoke the server application.

User can get more and detailed application options as following by invoking

`aibox-dist-cam --help`

```
Usage:
  aibox-dist-cam [OPTION?] - Application for facedetion detction on SoM board of Xilinx.

Help Options:
  -h, --help                        Show help options
  --help-all                        Show all help options
  --help-gst                        Show GStreamer Options

Application Options:
  -m, --mipi=                       use MIPI camera as input source, auto detect, fail if no mipi connected
  -u, --usb=media ID                usb camera media device id, e.g. 0 for /dev/media0
  -f, --file=file path              location of h26x file as input
  -i, --infile-type=h264            input file type: [h264 | h265]
  -W, --width=1920                  resolution w of the input
  -H, --height=1080                 resolution h of the input
  -r, --framerate=30                framerate of the input
  -c, --camid=0                     cam ID
  -l, --fileloop=loop file source   loop file source 
  -o, --outmedia-type=h264          output file type: [h264 | h265]
  -p, --port=554                    Port to listen on (default: 554)
  -n, --nodet                       no AI inference
  -A, --audio                       RTSP with I2S audio
  -R, --report                      report fps
  -a, --noattach                    Not attach AI info, default: true
  --ROI-off                         turn off ROI
  --control-rate=low-latency        Encoder parameter control-rate
  --target-bitrate=3000             Encoder parameter target-bitrate
  --gop-length=60                   Encoder parameter gop-length
  --profile                         Encoder parameter profile.
  --level                           Encoder parameter level
  --tier                            Encoder parameter tier
  --encodeEnhancedParam             String for fully customizing the encoder in the form "param1=val1, param2=val2,...", where paramn is the name of the encoder parameter
```

### Start The Client Application

#### Command Line

**Note** The application needs to be run with ***sudo***.

#### Examples

  ```bash
  sudo aibox-dist-monitor -s rtsp://address-of-cam-1 -t r -s rtsp://address-of-cam-2 -t r -S 1
  ```

**Note** Above example is for live steam capturing from camera, if you are starting with the video file streaming way which we recommended as a start point, please **remove the option "-S 1"** as.

  ```bash
  sudo aibox-dist-monitor -s rtsp://address-of-cam-1 -t r -s rtsp://address-of-cam-2 -t r
  ```

**Note**: Only one instance of aibox-dist-monitor application can run at a time because it requires exclusive access to a DPU engine and there is only one instance of DPU that exists in the aibox-monitor platform.

##### Command Options

User can get more and detailed application options as following by invoking

`aibox-dist-monitor --help`

```
Usage:
  aibox-dist-monitor [OPTION?] - AI Application of pedestrian + reid + tracking for multi RTSP streams, on SoM board of Xilinx.

Help Options:
  -h, --help                                                        Show help options
  --help-all                                                        Show all help options
  --help-gst                                                        Show GStreamer Options

Application Options:
  -s, --src=[rtsp://server:port/id |file path]                      URI of rtsp src, or location of h264|h265 video file. Must set. Can set up to 4 times
  -S, --syncType                                                    Sync Type: 0: Sync for file based stream; 1: Sync for live stream
  -I, --indTol                                                      Tolerance value used by syncType 0: number of frame
  -T, --timeTol                                                     Tolerance value used by syncType 1: microsecond
  -e, --srcenc=[h264|h265]                                          Encoding type of the input source. Optional. Can set up to 4 times.
  -p, --pos=[0|1|2|3]                                               Location of the display in the 4 grids of 4k monitor. Optional. 0: top left, 1: top right, 2: bottom left, 3: bottom right. Optional. Can set up to 4 times.
  -r, --framerate=auto                                              Framerate of the input. Optional. Can set up to 4 times.
  -R, --report                                                      Report fps
  -c, --config=/opt/xilinx/kv260-aibox-dist/share/vvas/cam_setup.json     System calibration json file path

```

## Files structure

The application is installed as:

* Binary File Directory: /opt/xilinx/kv260-aibox-dist/bin

  | filename | description |
  |----------|-------------|
  |aibox-dist-cam | app for camera board|
  |aibox-dist-monitor | app for monitor board|

* Configuration file directory: /opt/xilinx/kv260-aibox-dist/share/ivas/

  | filename | description |
  |-|-|
  |cam_setup.json       |           Config of camera set up and calibration.
  |ped_pp.json       |           Config of refinedet preprocess.
  | refinedet.json   |           Config of refinedet.
  | crop.json        |           Config of cropping for reid.
  | reid.json        |           Config of reid.

*  Model files: => /opt/xilinx/kv260-aibox-dist/share/vitis_ai_library/models

  The model files integrated in the application use the B3136 DPU configuration.

  |      foldername      | description |
  |----------------------|-------------|
  |person-orientation_pruned_558m_pt | Model files for personal orientation|
  |personreid-res18_pt   | Model files for ReID |
  |refinedet_pruned_0_92 | Model files for refinedet |
  |FairMot               | Model files for pedestrian detection|


<p align="center"><sup>Copyright&copy; 2021 Xilinx</sup></p>
