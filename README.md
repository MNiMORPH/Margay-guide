# Margay-guide
A guide on setting up and potentially troubleshooting a Margay data logger. A more thorough version of this exists [here](https://northernwidget.com/tutorial/), but my guide is written from the perspective of someone who's new to using Arduino boards and shows some of the issues/questions I had when getting started. 

## Physical materials needed

I started by gathering all the materials I'd need.

- Margay data logger (Mine was ATMega 1284p 8 MHz)
    - This is a small data logger that will eventually collect data in the field, but it needs to be set up and programmed before it can do that.
- Programmer (Mine was the AVR ISP mkll)
    - This is a device that will allow us to **burn a bootloader** (I'll define this below) onto the Margay board.
- USB-B cable
    - This is used to connect the programmer to the laptop. 
- USB-C cable
    - This is used to connect the logger to the laptop after initially burning a bootloader, and will keep the logger connected to power. In the field, a battery would supply power.
- My laptop
    - To download softwares and upload programs to the Margay logger.
 
<div>
    <figure>
        <img src="https://github.com/user-attachments/assets/94785e2c-cfe8-4ff8-8d98-bc7777cd5f79" style="float:left; width:300px; height:300px; overflow:hidden" alt="Choosing driver in Zadig"/>
        <figcaption> Margay logger </figcaption>
    </figure>
    <figure>
        <img src="https://github.com/user-attachments/assets/e82422a4-9de9-45f3-a0f7-4397bf304e4e" style="float:right; height:300px" alt="Choosing driver in Zadig"/>
        <figcaption> Programmer </figcaption>
    </figure>
</div>
<div clear: both> </div>

### What is "burning a bootloader"? 

This just means that you make your Arduino board capable of receiving computer programs through a USB cable. You do not have to make any physical changes on the board to enable this. All you have to do is connect the board to your computer using the programmer (mentioned in the materials list) and install/configure some software on your computer. Then the board will have this ability from now on, so you won't have to do this more than once.

Most of this guide is about what you need to download on your computer in order to do this, and how you need to configure it.

## Software and library downloads

A quick list of downloads I made, before going into more detail on each:
- Arduino IDE
- Northern Widget libraries
    - Git Bash (used to install the libraries)
- SetTimeGUI
    - Processing

I downloaded the [Arduino IDE](https://www.arduino.cc/en/software), which will be used to write programs in Arduino and upload them to the logger.

Next, I downloaded the custom Northern Widget libraries found at the link https://github.com/NorthernWidget/NorthernWidget-libraries. There are detailed instructions in the readme, but I'll summarize here. First, I found where my Arduino libraries folder was. The standard location on Windows should be **My Documents\Arduino\libraries**. You can also find out by going to **File > Preferences** in the Arduino IDE, and looking at the Sketchbook location towards the top. Your libraries folder should be inside your sketchbook. For me, there was an Arduino folder in My Documents, but it was empty (there was no libraries folder inside). So, I created a "libraries" folder myself.

I downloaded the Northern Widget libraries using git so that updating them would be easier in the future, but had to download Git first ([available here](https://git-scm.com/downloads)). In the Git Bash application, I changed directories into my Arduino libraries folder, then went back to my browser and copied the link from the [NorthernWidget-libraries repository](https://github.com/NorthernWidget/NorthernWidget-libraries). (I clicked on the green **Code** button at the top of the page, then copied the link under the HTTPS option. More instructions for cloning in git are [here](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository).) 
<div>
    <img src="GithubClone.png" style="float:left; width:300px" alt="Clone link in GitHub"/>
</div>
<div clear: both> </div>

In Git Bash, I entered 

`git clone https://github.com/NorthernWidget/NorthernWidget-libraries.git` 

using the link I had just copied. When I checked my Arduino libraries folder again, it now had a folder inside it called **NorthernWidget-libraries**. However, this folder had multiple subfolders inside, each its own library (for example, the Margay library folder is inside the Northern Widget library folder.) For the Arduino IDE to recognize the individual libraries, take the subfolders out of the NorthernWidget folder, and move them directly into the Arduino libraries folder. You can check whether it worked by hovering over **Sketch > Include Library** in the IDE, and seeing which libraries you've added. 

_Note: At the time of writing this, one of the Northern Widget libraries (MCP3421) has the same name as a default Arduino library. Sometimes when I open the IDE, it tells me that updates are automatically available for this library, which really means it's trying to replace the Northern Widget version with the default Arduino version. This will cause issues later. So, don't accept any updates that it tries to push for MCP3421. You can see which libraries it's trying to update by clicking "Install Manually" when you get that notification._

The last thing I downloaded was the SetTimeGUI, with download and running instructions on [this page](https://github.com/NorthernWidget/SetTime_GUI). For now, though, all I did was click the green Code button on that page and used `git clone` to download the files in the repository (or you could download the zip file.) I needed to download [Processing](https://processing.org/download) too, in order to eventually run SetTime.

We should now have all the downloads we need, and only need to change some settings before we can start programming.

## Board, programmer, and port definitions
All of these changes are made in the Arduino IDE. 

Since I was using a Margay data logger (a board compatible with Arduino but considered third-party instead of a standard Arduino board), I needed to configure some settings so that the IDE would recognize the board. A [guide](https://github.com/NorthernWidget/Arduino_Boards) for this already exists (with pictures), but I'll still summarize the steps I took. 

I went to **File > Preferences** in the IDE, and a window popped up, with "Additional Boards Manager URLs" towards the bottom. The field to enter text was empty for me at first, but I copy-pasted the following URL into that field and saved changes with **OK**. 
######
        https://raw.githubusercontent.com/NorthernWidget/Arduino_Boards/master/package_NorthernWidget_index.json

Then, I went to **Tools > Board > Boards Manager**, and it opened a panel on the side of the screen. I typed in "Northern", and the option for Northern Widget Data Logger AVR Boards came up. I clicked "Install". 

Now it was time to select which board I was using. The Margay logger has two versions: ATMega1284p 8MHz (the current version, so it's most likely this one), or ATMega644p 8MHz (older). If you're not sure, this is printed in really small font on the logger (I circled it in red in the picture below.) Mine was the 1284p. I made this selection in the IDE by going to **Tools > Board > Northern Widget Data Logger AVR Boards > ATMega1284p 8MHz**. 

<div>
    <img src="https://github.com/user-attachments/assets/e0cb7d76-0d3d-4603-87cb-fc79783bca4d" style="float:left; width:400px" alt="Front of Margay logger"/>
</div>
<div clear: both> </div>

I also had to tell the IDE which programmer I was using. The programmer is only necessary if this is the first time you're using your data logger, since we use the programmer to burn a bootloader (and again, we only have to do this once). The programmer should have a label on it to let you know which one it is - mine was the AVR ISP mkII. I selected this in **Tools > Programmer**.

## Burning the bootloader

Use the USB-B cable to connect your programmer to your computer. Also plug the programmer's ribbon cable into the Margay board, where the 2x3 pin header is. The ribbon should lie across the board rather than bumping against the ridge at the end of the board. However, it can be easy to mix this up, so just try again the other way if burning the bootloader fails.

Then go to **Tools > Burn Bootloader** in the Arduino IDE, and you should find out within a few seconds whether it worked. 

#### Troubleshooting

If it didn't work, the most common reason is that the programmer's cable is plugged into the board the wrong way. Flip it around and try again.

I was getting an error message relating to the USB port/driver in my laptop where the cable was plugged in. The option for me to select which port I was using in the IDE was grayed out. I downloaded [Zadig](https://zadig.akeo.ie/), which installs a USB driver. Once the Zadig window comes up, use the up/down arrows to select a driver. I chose libusbK. 
<div>
    <img src="https://github.com/user-attachments/assets/53f41b1f-6303-4690-9f7a-5298106a26b4" style="float:left; width:400px" alt="Choosing driver in Zadig"/>
</div>
<div clear: both> </div>

Back in the Arduino IDE, I now had the option to select a port. Only one option came up for me: COM3, so I selected this, tried to burn a bootloader again, and this time it worked.

## Testing

To test whether burning the bootloader had worked, I uploaded an example program to the board. I disconnected the programmer, and instead used the USB-C cable to connect the logger to my computer. 
<div>
    <img src="https://github.com/user-attachments/assets/340621e1-55e9-4aee-bd74-4923eeb59641" style="float:left; height:300px" alt=”Plugged in logger”/>
</div>
<div clear: both> </div>

A simple example program to test was at **File > Examples > 01.Basics > Blink**. As its documentation says, it turns the LED on the board on for a second, and off for a second repeatedly. I uploaded this to the logger by going to **Sketch > Upload**, and saw that the Arduino LED was blinking like expected.

### _Margay-compatible program_

You'll also need to upload a Margay-compatible program to the logger before you can set its clock. A Margay-compatible program is anything with the line `#include "Margay.h"`. 

One of these programs can be found by going to **File > Examples > Margay_Library > MargayDemo** in the Arduino IDE. Compile and upload this to the logger, and the LED may show a series of different colors. You can find out what they mean [here](https://github.com/NorthernWidget/Margay_Library). 

#### Troubleshooting

At the time that I compiled MargayDemo (August 2024), there were a few small syntax errors involving capitalization. These will probably be changed by now, but in case there's another program you're trying to compile that hasn't been updated, this is what to do if you get these error messages. 
- "Model_0v0" should be "MODEL_2v2". (You can also go into Margay.h (which you can open with the Notepad app on Windows), and the different options for the model versions are under "enum board".) This error was in line 13 of MargayDemo, which at the time said `Margay Logger(Model_0v0);`.  This is what the error said:
    ```
     13:15: error: 'Model_0v0' was not declared in this scope
     Margay Logger(Model_0v0)
     13:15: note: suggested alternative: 'MODEL_0v0'
     Margay Logger(Model_0v0);
                   ^~~~~~~~~
                   MODEL_0v0
    ```
- "Logger.Run" should be "Logger.run". This error happened in line 21, which originally read `Logger.run(Update, UpdateRate);`, and the error message was:

    ```
    21:10: error: 'class Margay' has no member named 'Run'; did you mean 'run'?
    Logger.Run(Update, UpdateRate);
          ^~~
          Run
    ```
- This last error was the result of me automatically saying yes when Arduino asked me if I wanted to install the available updates for my libraries (it asks this when I open the IDE). I mentioned this in the "Software and library downloads" section, but the Northern Widget libraries include a library called MCP3421, which shares a name with Arduino's built-in MCP3421 library. The NW version has version number 1.0.0, and the Arduino version has number 1.0.4 (at the time of writing this), so it thought I was running an older version of its built-in library. When I accepted the update, it replaced the Northern Widget file with the default Arduino file. This is the error message I got as a result:

  ```
   156:3: error: 'MCP3421' does not name a type; did you mean 'CMCP3421'?
   MCP3421 adc;
   ^~~~~~~
   CMCP3421
   ```
If your error message mentions MCP3421, open the file MCP3421.h inside your libraries location, and read the documentation. If it says Dirk Ohme created it, you have the built-in version (the wrong version), so delete it and re-install the Northern Widget MCP3421 library. The NW version of MCP3421.h should say it was written by Bobby Schulz.

### SetTime GUI

We downloaded this earlier, and now we can actually run it to set the time on the logger's clock. While your Margay logger is still plugged into your computer, open Processing (which we also downloaded earlier) and pull up the program NW_Logger_TimeSet.pde. Run this, and a window should appear on your screen with the computer time, logger time, and difference between them. Click the button to set the logger time, and the logger time should now match your computer time. Then you can close the program and unplug your logger. A video with all of these instructions is [here](https://youtu.be/q0fVwhMNLZg).

#### Coin-cell battery

When setting the clock, insert the coin-cell battery into the logger. This doesn't take the place of battery power, but it's just so that the time you set will stay on the logger.
<!-- include a picture --> 

## Preparing for deployment

### The SD card
Insert this into the logger to record data, then insert it into your computer to read the data. You'll use a USB-SD card reader in order to insert it into your computer. Once you have done this, the data will appear on your computer as a text file. 

<div>
    <figure>
        <img src="https://github.com/user-attachments/assets/cb14742a-a759-43d9-a3e9-4d3065692ad1" style="float:left; width:200px" alt="SD card setup"/>
        <figcaption> The SD card will insert into the silver slot on the logger. The SD card reader is in bottom right and has a slot in the side for the SD card. </figcaption>
    </figure>
</div>

<div clear: both> </div>

After running MargayDemo with the SD card in, the data was saved in [this text file](NoSensors.txt).

### Battery power

It is powered by 3 AA batteries and needs at least 3.3 V to continue logging. The red and black wires connect to B+ and B-, respectively.
<!-- picture of how it attaches, with screwdriver and side green cage -->

### Sensors

#### BME 280 sensor
This is an external sensor you can attach to read temperature, pressure, and humidity. The wires should be inserted in the same place for both the sensor and the board: the black connects "GND" to "GND", green goes from "SCK" on the sensor to "SCL" on the board, and white from "SDI" to "SDA".

<div>
    <img src="https://github.com/user-attachments/assets/1be07783-75ea-4def-a1d5-b451ca918b99" style="float:left; width:400px" alt="BME 280 setup"/>
</div>

<div clear: both> </div>

Northern Widget has Arduino programs for deployments as well, linked [here](https://github.com/NorthernWidget/Deployments/tree/master/Margay). I ran the one in the BME280 folder. Now the text file from the SD card has two measurements of temperature, one from the on-board sensor and one from the external one. [Here](BME280.txt) is an example text file of what that looks like.

#### Connecting to a Monarch

The Monarch logger is a pyronometer measuring incoming radiation in UV, visible, and infrared wavelengths. Its repository can be found [here](https://github.com/NorthernWidget-Skunkworks/Project-Monarch/tree/master). You can connect a Margay logger to a Monarch sensor to have these additional fields in your data.

You need to upload a program (also known as firmware) to a Monarch logger so it can take data, but you do *not* need to bootload the Monarch. You can just upload the program right away. Since it can't communicate using USB, you'll use the same programmer that was used earlier in this guide. 
<!-- link here -->
In the Arduino IDE, I opened the program v1_B. I needed to select what board I was using, as the Monarch is part of the ATTinyCore group. Just like how I added board definitions for the Margay in the earlier section
<!-- link here -->
, I also needed to add board definitions for the Monarch. I followed [these instructions](https://northernwidget.com/tutorial/#add-board-definitions-for-the-attinycore-microcontrollers), but basically I just went to **File > Preferences** and found "Additional boards manager URLs". Since I already had one URL in there, I clicked the button with the two overlapping rectangles, and entered the URL for the Monarch boards on a new line. The URL is: http://drazzy.com/package_drazzy.com_index.json
In **Tools > Board > Boards Manager**, I searched for ATTinyCore and installed the one by Spence Konde. Now, I could select the board and programmer I was using. The board name for the Monarch is ATtiny841 (Micronucleus / Wattuino). Then, I chose **Sketch > Upload Using Programmer**. Even if it works, lights won't flash on the Monarch, but the IDE will still tell you if it was uploaded successfully.

Then, I went back to the Margay and uploaded a program that would tell it to communicate with the Monarch. 
<!-- what program to upload to the Margay, and how to wire them together -->
<!-- 6-pin header -->
<!-- pictures of the finished setup -->











