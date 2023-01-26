# COBIRAS: Maximizing Bandwidth Utilization in DASH By Minimizing Off-Phases and Offering a Continuous Bit Rate Slide

This repository contains the docker container setup accompanying the paper **COBIRAS: Maximizing
Bandwidth Utilization in DASH By Minimizing Off-Phases and Offering a Continuous Bit Rate Slide**
submitted for review to MMSys'23. It is able to run and log metrics for DASH video streaming via
the [dash.js video player](https://github.com/Dash-Industry-Forum/dash.js/).

## Prerequisites

- recent `docker` and `docker-compose` installation, as well as the `bash` shell
- ~50GB of storage
- sufficient CPU power to encode AVC encoding up to 1440p with >1x speed (Only required if you want
  to run JITE)

## Concrete Build and Run instructions

## Prerequisites

- cd to `prepare-video-files/` and execute the `prepare_video_files.sh` script:
  ```
  bash prepare_video_files.sh
  ```
  The script will download the Tears of Steel video in 4k quality (about 6.3GB) from a official
  mirror and encodes it to 49 different representations. Six of the representations will be used for
  the Default DASH runs, while the additional 43 representations will be used in the ManyReps DASH
  runs, simulating continuous quality levels. The representations will be dashed and moved to
  folders in the `DASH-setup` directory. You can gather detailed ffmpeg encoding settings from
  the `encode()` function, as well as MP4Box dash settings from the `dash()` function.

> There is a `cleanup.sh` file, which can be used to clear the `prepare-video-files` directory.
> Depending on your machine's compute power the encoding can take a while.

## Running

The testbench can be executed by running the `exec_setup.sh` in the `DASH-setup` directory:

  ```
  cd DASH-setup
  bash exec_setup.sh
  ```

The script will create three Docker containers via the `docker-compose.yml` file and the Dockerfiles
in the `client/`, `netem/` and `server/` directories. Then it will start playing the Tears Of Steel
video on different network traces, ABRs and streaming configurations. A run with the ToS video on a
network trace takes about 15 minutes, logged metrics will be saved in the `logs/` directory,
grouped by the ABR used.

### Locations

|                              |                                                                 |
|------------------------------|-----------------------------------------------------------------|
| Code of ABR MinOff           | `DASH-setup/server/data/public/javascripts/customBufferRule.js` |
| Network Traces               | `DASH-setup/netem/data/trace_files`                             |
| dash.js player configuration | `DASH-setup/server/data/public/javascripts/player.js`           |
| node.js web server           | `DASH-setup/server/data/app.js`                                 |
| Logs of past runs            | `DASH-setup/logs/<ABR>/`                                        |

## Configuration

While in the current state this setup renders the test data from the mentioned paper, it can
easily be adjusted to run different DASH configurations, testdata and network traces.

|                                  |                                                                                                                                                                                                                                                                                                                   |
|----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Change network traces            | Edit path of network traces in `exec_setup.sh:15`                                                                                                                                                                                                                                                                 |
| Only run speciffic/different ABR | Edit ABR list in `exec_setup.sh:18`                                                                                                                                                                                                                                                                               |
| Run only JITE                    | Edit video list in `exec_setup.sh:17`. JITE can be identified by `_runtime` in the video name. `_pre` ist perfect JITE (all representations are pre encoded)                                                                                                                                                      |
| Use different video              | Create DASH segments for all video representations. Put them in a directory in `DASH-setup/server/data/public/videos/`. Edit video list in `exec_setup.sh:17`. If you want to use JITE, the node.js server must be adjusted accordingly. Depending on our Manifest structure, other adjustments might be required |

