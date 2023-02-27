---

author : skuodi
title: "Powerful Interrogation: Getting a Smart Battery to spill the beans"
comments : true
toc: true
toc_sticky: true
header:
  teaser: /assets/images/smart_battery_teaser.jpg
tags:
    - Hacks
gallery:
    - url: /assets/images/smart_battery_hardware.jpg
      image_path: /assets/images/smart_battery_hardware.jpg
      alt: "smart battery from laptop" 
      title: "Smart Battery terminals. The P+ and P- are labelled"
    - url: /assets/images/smart_battery_hardware1.jpg
      image_path: /assets/images/smart_battery_hardware1.jpg
      alt: "laptop battery" 
      title: "Smart Battery from laptop" 
    - url: /assets/images/smart_battery_hardware2.jpg
      image_path: /assets/images/smart_battery_hardware2.jpg
      alt: "breadboard connection" 
      title: "Smart Battery breadboard connection" 
    - url: /assets/images/smart_battery_hardware3.jpg
      image_path: /assets/images/smart_battery_hardware3.jpg
      alt: "breadboard connection" 
      title: "Smart Battery breadboard connection" 
    - url: /assets/images/smart_battery_hardware4.jpg
      image_path: /assets/images/smart_battery_hardware4.jpg
      alt: "breadboard connection" 
      title: "Smart Battery breadboard connection" 
gallery1:
    - url: /assets/images/smart_battery_result.png
      image_path: /assets/images/smart_battery_result.png
      alt: "arduino serial monitor output showing printed values from the smart battery" 
      title: "Arduino serial monitor output"
    - url: /assets/images/smart_battery_result1.png
      image_path: /assets/images/smart_battery_result1.png
      alt: "logic analyser output showing values from the smart battery" 
      title: "Logic analyser output"
    - url: /assets/images/smart_battery_result2.png
      image_path: /assets/images/smart_battery_result2.png
      alt: "arduino serial monitor output side by side with logic analyser output" 
      title: "Serial monitor vs logic analyser output"
    - url: /assets/images/smart_battery_result3.png
      image_path: /assets/images/smart_battery_result3.png
      alt: "logic analyser output showing printed values from the smart battery in hexadecimal format" 
      title: "Querying the device chemistry"

---

# Powerful Interrogation: Getting a Smart Battery to spill the beans

The battery in your smartphone or laptop knows a lot more about itself than we might have imagined, thanks to a tiny chip embedded inside the battery that monitors and records the state of the battery for the duration of the battery\'s lifetime. We might not be conducting any blacksite operations today but we\'ll be having a one-on-one conversation with a laptop battery to find out about it\'s life experience.

## Definition of terms
A **battery** is a collection of cells that store electrical energy, connected together to produce energy in form of electricity. Batteries have been of tremendous use in the development of technologies such as portable electronics, renewable energy storage such as solar power systems and electric vehicle technologies. There exists batteries of all shapes, sizes and chemistries, each designed for a specific use case. Dry cells (zinc-carbon or alkaline) are the most common type used in many household electronics such as tv remotes and clocks which have low power requirements. They are non-rechargeable, so once they run out of power, they\'re down for the count.

With the advent of Very Large Scale Integration (VLSI) more and more transistors (running into the billions e.g. for a laptop CPU) can now be packed into a single chip. More MOSFETS in a single package means the power input to the chip has to supply a higher current to overcome the large total gate capacitances of many transistors switching at the same time. Therefore, modern portable devices such as mobile phones and laptops have much higher power requirements than ever before, presenting a big hurdle in their design for portability. Dry cells would be a poor waste of resource as they would make they would run out quickly and be an environmental menace to dispose of.

Enter rechargeable batteries; like the lead-acid type used in cars. Though these can supply a very large current, they are bulky and high-maintenance - unsuitable for portable technology. Lithium batteries present the best option for portability: they have the highest power density, charge quickly and are much smaller. If you\'re reading this article on a rechargeable device, then it most certainly packs a lithium battery. But as with all \'amazing\' things, there\'s a catch: **safety**. Look up _"lithium battery hazards"_ on youtube and you\'ll find all manner of videos that giving testament to the danger involved in the handling of lithium cells.

That said, you\'re probably thinking, _if, despite all the danger, lithium battteries are everywhere, then someone must have found a way to use them safely_. And you\'d be right. The solution is to have a [microcontroller](https://wikipedia.org/wiki/Microcontroller) built-in to the battery that monitors the charge, discharge, temperature and capacity of the cell, only allowing the battery to be charged or discharged when it is safe to do so; so is born the [Smart Battery](https://wikipedia.org/wiki/Smart_Battery). This, of course doesn\'t protect the battery from extreme conditions such as piercing or being thrown in fire - though in such cases, I don\'t believe battery is the problem.

## Communication standards/specs

As with people joining hands to solve a problem, different electronic parts comprising a system need to have an effective means of communication before any task can be accomplished. To this end, many different electrical communication standards have been defined to cater for different requirements like electrical noise immunity(e.g. [CAN](https://wikipedia.org/wiki/CAN_bus) for industry), speed(e.g. [NVME](https://wikipedia.org/wiki/NVM_Express) for [SSDs](https://wikipedia.org/wiki/Solid-state_drive)), ease of implementation (e.g. [SPI](https://wikipedia.org/wiki/Serial_Peripheral_Interface) in microcontrollers), pincount (e.g [I2C](https://wikipedia.org/wiki/I%C2%B2C)) e.t.c. These standards are officially defined in specification documents that are most commonly of three types, for each standard:

1. **electrical specification**
	- defines voltages, logic level sequences and timings for electrical signals 
2. **data specification**
	-	defines the arrangement and meaning of data to be transmitted via the interface
3. **mechanical specification**
	- defines connector shape, dimensions, pincount and pin arrangement for devices using the interface

These are the basic documents, however, some specs go as far as to define the device driver formats (how the software controlling the interface should work). Many of these specifications are available at fee required to download the document or to join the *implementers forum* (group of people that meets to discuss and agree on actually design these standards). Each interface has an implementers forum and usually also a website that provides news and updates, as well as documents, on the implementation of the specification. Luckily, the specs we use for this tutorial are available free of charge.

## Smart Battery Specification
Smart Batteries require a communication interface that is effective and reliable for short-range communication with the system they power. This allows the system to monitor the state of charge (usually displayed as battery) and receive warnings from the Smart Battery if, for example, the battery temperature is beyond the safe limit. And effective indeed it is; the Smart Battery Standard v1.1 defined in 1998 is the very same standard used in today\'s high-end portable electronics!

The [Smart Battery System](https://en.wikipedia.org/wiki/Smart_Battery_System)(SBS) presents solutions to three main problems plaguing rechargeable battries at the time (1998):
- batteries present an unpredictable source of power as the user has no advance knowledge that the battery is about to run out.
- equipment powered by the battery cannot determine whether the present state of the battery can support an additional load
- batteries of different chemistries need to be charged in a specific sequence lest they be damaged

The microcontroller on a Smart Battery solves all these issues by keeping track of battery state and reporting said state to the host device. The latest Smart Battery specification can be found at the [Smart Battery System Implementers Forum website](http://www.sbs-forum.org/specs/).

## Let the interrogation begin!
Prerequisite to communication with the Smart Battery, or to any communication for that matter, we must understand the protocol that the Smart Battery speaks; only then we can we understand what we are required to do on our end. Actual electrical communication with a Smart Battery is built upon the [System Management Bus (SMBus) standard](https://wikipedia.org/wiki/System_Management_Bus) which is defined in [it\'s own specification](http://www.smbus.org/specs/spec_content.htm#Updates). SMBus is, in turn, an extension of the very popular [I2C standard](https://wikipedia.org/wiki/I%C2%B2C), also defined in [it\'s own standard](https://www.i2c-bus.org/specification/). It might be pretty far down a rabbit hole but this is good news for us; microcontrollers like the Arduino Uno have built-in I2C communication hardware which if we build upon, we\'ll be able to communicate with the Smart Battery. 
Feel free to look through the [I2C spec](https://community.nxp.com/pwmxy87654/attachments/pwmxy87654/nxp-designs/931/1/UM10204.pdf), [SMBus spec](http://www.smbus.org/specs/SMBus_3_2_20220112.pdf) and [Smart Battery spec](http://www.sbs-forum.org/specs/sbdat110.pdf) and try to implement it on an Arduino (or more advanced) board, hopefully in a more efficient way than I have.

### Hardware Setup

We\'ll need the following:
1. Arduino Board with an _ATMEGA_ chip onboard.
	- I tested it on an Arduino Pro Micro but it should work on Uno, Mega, Nano, Due and Leonardo as well with simple change of pinout

2. Smart Battery e.g laptop battery that\'s functional. 
	- It\'s possible to communicate with one that\'s depleted but that\'s beyond the scope of this tutorial
3. 2 x 1K Ohm resistors
4. Breadboard
5. Jumper wires
6. Breadboard power supply, for the microcontroller.

{% include gallery id="gallery" caption="Hardware Setup" %}

Connect them as follows:
- Arduino SDA pin to Smart Battery DAT pin and through a 1k resistor to +5V
- Arduino SCL pin to Smart Battery CLK pin and through a 1k resistor to +5V


|	Arduino  |Smart Battery|
|------------|-------------|
|	 SDA	 |	  DAT	   |
|	 SCL	 |	  CLK      |

Finding the _DAT_ and _CLK_ pin is more a game of reial and error. Below is  a labelled circuit board I extracted from an old battery. I\'ve found this pinout works for HP laptop batteries.

![HP Battery Pinout]({{ site.url }}{{ site.baseurl }}/assets/images/smart_battery_teaser.jpg "HP Battery Pinout")


| PCB Marking  | Pin Name | Description |
|------------|-------------|--------|
| P+  | Battery + | Battery power input/output terminal. Usually 2 pins |
| SMC | SMB_CLK   | SMBus Clock pin |
| SMD | SMB_DAT   | SMBus Data pin |
| RTC | SMB_T Pin | SMBus thermistor alert pin|
| BI  |	Battery Inserted/System present| Used to detect that the battery has been connected to a system |
| P-  | Battery - | Battery power/communication reference ground. Usually 2 pins |


### Software setup

For the more goal-oriented among us, I\'ve already implemented the SMBus and Smart Battery stack which is available at the [SBS_SMB project repository](https://github.com/sbs_smb), structured into two translation units as follows:

- **smbus_if** - an implementation of SMBus v3.1 using AVR\'s TWI pripheral.

- **sbs_smb** - implementation of Smart Battery Specification(SBS) v1.1 based on smbus_if

![SBS_SMB stack]({{ site.url }}{{ site.baseurl }}/assets/images/smart_battery_stack.png "SBS_SMB stack")

The library is written for AVR but can easily be [ported](https://wikipedia.org/wiki/Porting) to your preferred platform. Porting instructions are in the [readme in the repo](https://github.com/skuodi/sbs_smb#porting).

To get started with the software:

1. Having already installed the Arduino IDE, clone the **sbs_smb** repo to a local folder on your computer and copy it to your Arduino libraries folder:
`{YOUR_USER_NAME}` > `Documents` > `Arduino` > `libraries` > `sbs_smb`.

2. Start the IDE and if open, close and reopen it

3. Open the example under `File` > `Examples` > `sbs_smb` > `ATMEGA32U4_SBS_SMB`

4. Select your board from the `Tools` dropdown menu and upload the example sketch to your board.

The example asks the Smart Battery for its voltage, state of charge(%), remaining capacity, cycle count(number of times it\'s been charged and discharged) and date of manufacture.

{% include gallery id="gallery1" caption="The gist of the conversation" %}

## Conclusion

Modern portable devices have high power requirements supplied by lithium cells which need to be handled with care and thus many have a microcontroller embedded inide them that monitors and reports the state of the battery. The journey to learning how to communicate with this microcontroller has revealed a whole new world of standards and specifications that help us understand the electronic world around us.

## References

1. [I2C spec](https://community.nxp.com/pwmxy87654/attachments/pwmxy87654/nxp-designs/931/1/UM10204.pdf)
2. [SMBus spec](http://www.smbus.org/specs/SMBus_3_2_20220112.pdf)
3. [Smart Battery spec](http://www.sbs-forum.org/specs/sbdat110.pdf) 
4. [ATMEGA32U4 Datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/Atmel-7766-8-bit-AVR-ATmega16U4-32U4_Summary.pdf)
