# UT23 Driverless Control Module v.1.1

By: Muaz Shash, Drivierless Team Member, University of Toronto Formula Racing Team

With the start of the 2023 Formula Student season, the University of Toronto Formula Racing Team (UTFR) has established a new department: Driverless. This team is responsible for developing an autonomous system that can operate our vehicle to score even more points at competitions and secure our spot on the podium. Like all other systems in our car, the driverless system requires an electronic controller to interface with the real world. The driverless controller is responsible for all critical control and safety decisions related to autonomous tasks, such as controlling visual and audio system status indicators, stepping up/down voltages, managing power delivery to steering, braking, and throttle, as well as communicating with the rest of the car's electric system over multiple CAN buses.

<img src="https://user-images.githubusercontent.com/59676679/223560323-b228d0bd-7772-495a-933f-2677bc976b82.png" width=50% height=50%>

The Driverless Control Module includes many different components, such as a Teensy 4.1, two CAN transceivers, a 12V switching regulator, a 5V linear regulator, a 3V3 linear regulator, and a series of relays and MOSFET circuits. In this article, I will provide a high-level overview of how the driverless controller manages power and responds to autonomous commands.

## Schematic Design

The driverless control module distributes four voltage levels: 14.8V, 12V, 5V, and 3.3V. The 14.8V comes straight from the battery and is intended for brakes, steering, and the shutdown circuit. The 12V is distributed to the system status indicator LEDs and speaker, the 5V is for the Teensy 4.1 microcontroller, and the 3.3V is for the CAN transceivers. You may also notice that there are plenty of LEDs and testing points. These are purely for debugging during testing and serve no other purpose.

![image](https://user-images.githubusercontent.com/59676679/223592019-46f80a8a-3806-4618-8f5a-b876acea4864.png)

The controller features many MOSFETs and relays to control power. The vehicle features a shutdown circuit (SDC) that essentially disables the car from moving if any point in the SDC has been opened. On the driverless controller, this corresponds to the wires in blue. If the 3.3V Autonomous System Enable (AS_EN) signal controlling the MOSFET is low, the vehicle will never be able to move. If the 14.8V Autonomous System Master Switch (ASMS) is high, then this implies we are in autonomous driving mode and require the Remote Emergency Stop (RES), which can be controlled wirelessly, to be closed. If the ASMS is low, then this implies we are in manual driving mode and we can bypass the Remote Emergency Stop. The ASMS also controls whether power can be delivered to the brakes and steering motors. All of this is done using a force-guided contacts relay to prevent the possibility of the autonomous system taking over during manual driving.

![image](https://user-images.githubusercontent.com/59676679/223591579-2339d455-6630-46a1-8d2b-aa1297611052.png)

The UTFR team uses the Teensy 4.1 microcontroller that boasts a 600 MHz ARM processor and 3 CAN bus channels. The Teensy is responsible for managing the vehicle start and steering relays using the MOSFETs in the image above, controlling the steering and brake over PWM, communicating over CAN to the RES and the rest of the car's electrical system, and controlling autonomous status system indicators (ASSI). The ASSI are a speaker and RGB strips located around the car. The Teensy generates a square wave signal to control the speaker and pulls the blue/green/red pins low to make the RGB strips flash blue or yellow.

![image](https://user-images.githubusercontent.com/59676679/223591611-9cb25c7b-3316-472c-b801-9936783d2f89.png)

In addition to simply sending out signals, the Teensy is programmed to monitor all of its signals to provide adaptive feedback. We constantly monitor the battery voltage to avoid loading too much stress on the battery and low voltage system throughout the car. We check brake voltage to see if autonomous braking is possible and brake pressure to see how hard we're pressing the brakes, and we read how much current is flowing through the autonomous system indicators to confirm the signal was sent and the connection is stable. Due to its robustness to noise, we also use the sensor CAN bus to read safety-critical sensor data collected from other parts of the car. Shown below is the UT23 CAN architecture diagram.

![image](https://user-images.githubusercontent.com/82067858/210156016-07d12071-6ed7-4ffc-8136-203345e28255.png)

## PCB Layout
<img src="https://user-images.githubusercontent.com/59676679/223591426-7f8ed4a5-1224-4959-9243-3dcb0d762767.png" width=50% height=50%>

The Driverless Control Module is a four-layer board designed using Altium Designer. The four layers include a high-frequency digital signal and power top layer, a power and ground second layer, a grounded third layer, and an analog and low-frequency signal bottom layer. Differential signals were routed near each other, and other high-frequency signals were routed in a manner that considered adequate spacing and reduced trace length. A substantial effort was placed in making a user-friendly silkscreen to ensure the correct and safe fabrication, assembly, and operation of the board.
 
## JLCPCB

This PCB is a key component of a rapid and thorough design cycle aimed at maximizing driver safety. The technical driver safety development within our team was only possible thanks to the help of JLCPCB (https://jlcpcb.com/RAT). Founded in 2006, JLCPCB (https://jlcpcb.com/RAT) is a leading global PCB manufacturer with over 16 years of experience providing the best customer experience through the rapid production of highly reliable and cost-effective PCBs.

![image](https://user-images.githubusercontent.com/82067858/210156022-70873878-5b80-4122-a691-4b0cda5d814c.png)
 
The Driverless Control Module was ordered from JLCPCB (https://jlcpcb.com/RAT) for quick, reliable, and cost-effective manufacturing. Ordering PCBs from JLCPCB (https://jlcpcb.com/RAT) is extremely simple and consists of the following four quick steps:

1.	Export Gerber files from Altium Designer
2.	Click on the instant quote button on the JLCPCB website (https://jlcpcb.com/RAT)
3.	Add your Gerber files and double-check the uploaded PCB in the Gerber viewer
4.	JLCPCB automatically detects all the general PCB specifications and fills out the order form for you. Review and modify these to ensure they match your PCB requirements.
5.	Click save to cart, fill out your address, select your shipping method, and pay

Furthermore, JLCPCB (https://jlcpcb.com/RAT) also provides many other manufacturing services including an SMT service which fulfills the money and time saving needs of customers at only an $8.00 setup fee ($0.0017 per joint). It is a one stop online platform where you can also track the progress of your PCBA manufacturing process, which is completed within a day, in real time. Through the platform you can conveniently source thousands of components from JLCPCB (https://jlcpcb.com/RAT) and its reliable component partners like Digikey and Mouser. The quality and reliability are always superb with professionally trained engineers, X-ray inspection, automated optical inspection, automated component placement, and various soldering techniques. 
