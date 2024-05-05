# David Attenborough narrates your life. 

https://twitter.com/charliebholtz/status/1724815159590293764

## Want to make your own AI app?
Check out [Replicate](https://replicate.com). We make it easy to run machine learning models with an API.

## Setup

Clone this repo, and setup and activate a virtualenv:

```bash
python3 -m pip install virtualenv
python3 -m virtualenv venv
source venv/bin/activate
```

Then, install the dependencies:
`pip install -r requirements.txt`

Make a [Replicate](https://replicate.com), [OpenAI](https://beta.openai.com/), and [ElevenLabs](https://elevenlabs.io) account and set your tokens:

```
export OPENAI_API_KEY=<token>
export ELEVENLABS_API_KEY=<eleven-token>
```

Make a new voice in Eleven and get the voice id of that voice using their [get voices](https://elevenlabs.io/docs/api-reference/voices) API, or by clicking the flask icon next to the voice in the VoiceLab tab.

```
export ELEVENLABS_VOICE_ID=<voice-id>
```

## Run it!

In on terminal, run the webcam capture:
```bash
python capture.py
```
In another terminal, run the narrator:

```bash
python narrator.py
```

## WSL2 
### install elvenlabs old package

```
pip3 install elevenlabs==0.2.27
```
### winusb

download and install on win [wslusbipd](https://github.com/dorssel/usbipd-win/releases)

add webcam to wsl from powershell:
```
usbipd list
usbipd bind --busid 4-4
usbipd attach --wsl --busid 4-4
```

### compile kernel with usb-cam modules
get the kernel source:
```
sudo apt update && sudo apt upgrade -y && sudo apt install -y build-essential flex bison libgtk2.0-dev libelf-dev libncurses-dev autoconf libudev-dev libtool zip unzip v4l-utils libssl-dev python3-pip cmake git iputils-ping net-tools dwarves
KERNEL_VERSION=$(uname -r | cut -d '-' -f 1)
sudo mkdir /usr/src
cd /usr/src
sudo git clone -b linux-msft-wsl-${KERNEL_VERSION} https://github.com/microsoft/WSL2-Linux-Kernel.git ${KERNEL_VERSION}-microsoft-standard && cd ${KERNEL_VERSION}-microsoft-standard
sudo cp /proc/config.gz config.gz
sudo gunzip config.gz
sudo mv config .config
sudo make menuconfig
# Build WSL2 kernel with usb camera support
# menuconfig -> Device Drivers -> Multimedia support -> Filter media drivers
#            -> Device Drivers -> Multimedia support -> Media device types -> Cameras and video grabbers
#            -> Device Drivers -> Multimedia support -> Video4Linux options -> V4L2 sub-device userspace API
#            -> Device Drivers -> Multimedia support -> Media drivers -> Media USB Adapters -> USB Video Class (UVC)
#            -> Device Drivers -> Multimedia support -> Media drivers -> Media USB Adapters -> UVC input events device support
#            -> Device Drivers -> Multimedia support -> Media drivers -> Media USB Adapters -> GSPCA based webcams
sudo make -j$(nproc)
sudo make modules_install header_install -j$(nproc)
sudo make install -j$(nproc)
sudo cp -rf bZimage /mnt/c/Sources/

# Update .wslconfig in $userdata%/.wslconfig
[wsl2]
kernel=C:\\Sources\\bzImage
```

before running activate uvcvideo module
```
modprobe uvcvideo quirks=128 nodrop=1 timeout=6000
```

