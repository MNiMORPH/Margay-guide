# Margay-guide
A guide on setting up and potentially troubleshooting a Margay data logger. A more thorough version of this exists [here](https://northernwidget.com/tutorial/), but this is written from the perspective of someone who's new to using Arduino boards and shows some of the issues/questions I had when getting started. 

### Physical materials needed

I started by gathering all the materials I'd need.

- Margay data logger (ATMega 1284p 8 MHz)
    - This is a small data logger that will eventually collect data in the field, but it needs to be set up and programmed before it can do that.
- Programmer (AVR ISP mkll)
    - This is a device that will allow us to **burn a bootloader** (I'll define this below) onto the Margay board.
- USB-C cable
    - I used this to connect my laptop to the logger after initially burning a bootloader, to keep the logger connected to power. In the field, a battery would supply power.
- My laptop
    - To download softwares and upload programs to the Margay logger.
 
<!-- insert pictures of the logger and the programmer here -->

### Software and Library Downloads

A quick list of downloads I made, before going into more detail:
- Arduino IDE
- Northern Widget libraries
    - Git Bash (used to install the libraries)
- SetTimeGUI
    - Processing

I downloaded the [Arduino IDE](https://www.arduino.cc/en/software), which will be used to write programs in Arduino and upload them to the logger.

Next, I downloaded the custom Northern Widget libraries found at the link https://github.com/NorthernWidget/NorthernWidget-libraries. There are detailed instructions in the readme, but I'll summarize here. First, I found where my Arduino libraries folder was. The standard location on Windows should be **My Documents\Arduino\libraries**. You can also find out by going to **File > Preferences** in the Arduino IDE, and looking at the Sketchbook location towards the top. For me, the Arduino folder in My Documents was empty (there was no libraries folder inside), but that was okay. 

I downloaded the Northern Widget libraries using git so that updating them would be easier in the future, but had to download Git first ([available here](https://git-scm.com/downloads)). In the Git Bash application, I changed directories into my Arduino libraries folder, then went back to my browser and copied the link from the [NorthernWidget-libraries repository](https://github.com/NorthernWidget/NorthernWidget-libraries). (I clicked on the green **Code** button at the top of the page, then copied the link under the HTTPS option. More instructions for cloning in git are [here](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository).) In Git Bash, I entered `git clone https://github.com/NorthernWidget/NorthernWidget-libraries.git` using the link I had just copied. When I checked my Arduino folder again, it now had a folder inside it called **NorthernWidget-libraries**.

The last thing I downloaded was the SetTimeGUI.

<!-- insert screenshot here? -->


### Board, Programmer, and Port Definitions
Since I was using a Margay data logger (a board compatible with Arduino but considered a third-party board), I needed to configure some settings so that the IDE would recognize the board. A [guide](https://github.com/NorthernWidget/Arduino_Boards) for this already exists (with pictures), but I'll still summarize the steps I took. 

I went to **File > Preferences**, and a window popped up, with "Additional Boards Manager URLs" towards the bottom. The field to enter text was empty for me at first, but I copy-pasted the following URL into that field and saved changes with **OK**. 
##
        https://raw.githubusercontent.com/NorthernWidget/Arduino_Boards/master/package_NorthernWidget_index.json

Then, I went to **Tools > Board > Boards Manager**, and it opened a panel on the side of the screen. I typed in "Northern", and the option for Northern Widget Data Logger AVR Boards came up. I clicked "Install". 

Now it was time to select which board I was using. The Margay logger has two versions: ATMega1284p 8MHz (the current version), or ATMega644p 8MHz (older). If you're not sure, this is printed in really small font on the logger (I circled it in red in the picture below.) Mine was the 1284p version. I made this selection by going to **Tools > Board > Northern Widget Data Logger AVR Boards > ATMega1284p 8MHz**. 

<!-- Insert picture of 1284p here -->










