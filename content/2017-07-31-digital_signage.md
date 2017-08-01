Title: Low cost Digital Signage with RaspberryPi and Screenly OSE
Date: 2017-07-31 20:00
Category: General
Tags: RaspberryPi
Summary: Awesome project that shows how to build cheap digital signage for your school, company or event.

#Low cost Digital Signage with RaspberryPi and Screenly OSE

This article will show you how everyone can easily build cheap digital signage project with minimal time and money investments

##1. Project description
If you need a digital signage solution which is low cost, open sourced, robust and easy to setup then you are reading right article. Screenly OSE will allow you create playlist with pictures, web-pages and even videos.
There are only few things that you will need for this project:

* [RaspberryPI](https://www.raspberrypi.org/) [3 Model B](https://www.raspberrypi.org/products/#buy-now-modal), which is $35 one board computer;
* Latest [Screenly OSE](https://www.screenly.io/ose/) custom image;
* A display or TV or any other screen with HDMI port;
* An SD Card (>4GB). Class 10 is highly recommended;
* An HDMI-cable;
* A network connection (with DHCP);
* A keyboard (needed for initial configuration)

##2. Installation
The fastest and easiest way to install ScreenlyOSE is by downloading custom image [here](https://github.com/screenly/screenly-ose/releases) (more ways of installation is described on [this page](https://www.screenly.io/ose/))

* Download Screenly OSE disk image from: [GitHub Repository](https://github.com/screenly/screenly-ose/releases);
* Write image to SD card either with [Etcher](https://etcher.io/)(recomended) or following official [instruction](https://www.raspberrypi.org/documentation/installation/installing-images/)(skip download image section and start with choosing OS of your operation PC)
* Once image is written onto SD card, please place into RaspberryPi slot, plug in HDMI, keyboard and power on your board 

##3. Tweaking configurations

###3.1 System configuration
If you successfully followed all steps in part 2, then now you should see screen similar to one below:

SCREEN PICTURE HERE

Press ```Ctrl+Alt+F1``` to show command line interface that look like this:

SCREEN PICTURE HERE

