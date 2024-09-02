# BroadcomSDR
Converting Broadcom chipsets in to SDR's to enable "proof of life" through broadcast arbitrary signals on WIFi bands

# Installation 

```
sudo apt-get install git gawk qpdf adb 
```

### If using an X86_64 Chip you also need to install 

```
sudo dpkg --add-architecture i386
sudo apt-get update
sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386
```

Clone Nexmon base repository 

```
git clone https://github.com/seemoo-lab/nexmon.git
```

Download and extract Android NDK r11c (use exactly this version!). 

Export the NDK_ROOT environment variable pointing to the location where you extracted the ndk so that it can be found by our build environment. 

```
cd seemoo-lab/nexmon/
```
```
source setup_env.sh
```
```
make
```

Navigate to Utilities 

```
make
```

```
make install
```

Navigate to patches/bcm4339/6_37_34_43/ and clone this repository: 

```
git clone https://github.com/seemoo-lab/mobisys2018_nexmon_software_defined_radio.git
```

Enter the created subdirectory mobisys2018_nexmon_software_defined_radio and run 

```
make install-rpi3plus
```

# What this achieves 

The firmware patch activates three ioctls:

    NEX_WRITE_TEMPLATE_RAM (426) writes arbitrary data into Template RAM that stores the raw IQ samples that we may transmit. The ioctl's payload contains (1) an int32 value indicating the offset where data should be written in Template RAM in bytes, (2) an int32 value indicating the length of the data that should be written and (3) the IQ samples as array of IQ values, where I (inphase components) and Q (quadrature components) are stored as int16 numbers.

    NEX_SDR_START_TRANSMISSION (427) that triggers the transmission of IQ samples. The ioctl's payload contains (1) an int32 value indicating the number of samples to transmit, (2) an int32 value indicating the offset where the signal starts in Template RAM, (3) an int32 value indicating a chanspec (channel number, bandwidth, band, ...), (4) an int32 value indicating the power index (lower value means higher output power), and (5) an int32 value indicating whether to loop over the IQ samples or transmit them only once.

    NEX_SDR_STOP_TRANSMISSION (428) stops a transmission started using NEX_SDR_START_TRANSMISSION.

## Transmitting a Signal 

The directory payload_generation contains the MATLAB script generate_frame.m that generates a Wi-Fi beacon frame with SSID MyCovertChannel. 

The generated IQ samples are written to a bash script that calls nexutil from the nexmon.org project to load the samples into the Wi-Fi chip's Template RAM by using ioctls. You can either generate your own signals or use the example myframe.sh file for transmitting the generated Wi-Fi frame. Copy myframe.sh to a directory that allows execution (such as /su/xbin/). To load the samples and start a single transmission, simply executute the bash script and observe the results by listening with a Wi-Fi sniffer on channel 1. 

A suitable Wireshark filter is wlan.addr == 82:7b:be:f0:96:e0. Of course, you are not limited to transmitting handcrafted Wi-Fi signals, you can transmit whatever you like in the 2.4 and 5 GHz bands. Nevertheless, you have to obey your local laws for transmitting signals, that might prohibit you to transmit any signal at all.


