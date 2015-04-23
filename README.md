# ibp-transducer-amp
A simple MRI compatible IBP transducer amplifier for use with my pressure trigger module

This project was initiated during the last week of my co-op term at SickKids hospital. The goal was to find a way to make the [Arterial-BP-MRI-Triggering-Unit](https://github.com/JoshBradshaw/Arterial-BP-MRI-Triggering-Unit) compatible with fluid filled invasive blood pressure transducers such as the Transpac IV. MRI compatible versions of these transducers have recently become available, and they are far less expensive than the solid state alternatives.

## The Transpac IV

This sensor was designed to be used in critical care clinical settings. The transducer setup is depicted in this excellent [video](https://www.youtube.com/watch?v=xgVhvuFgVbk). The Transpac IV uses the Freescale [MPX2300DT1
](http://www.freescale.com/webapp/sps/site/prod_summary.jsp?code=MPX2300DT1) to do the pressure measurement (I also included the datasheet in this repository just in case that link breaks). The freescale chip is extremely simple, it can be modelled as a [wheatstone bridge](http://www.analog.com/media/en/training-seminars/design-handbooks/49470200sscsect2.PDF) with four variable elements. 

The most important figures on the datasheet are:

1. Sensitivity = 5 uV / V excitation / mmHg
2. Maximum Pressure Range = -50 -- 300 mmHg
3. Recommended excitation voltage = 6V +/- 0.2V regulated

All of the other figures deal with the sensor's accuracy and drift over time and temperature, all of which are more than sufficiently good for our application.

The Transpac IV ships with short ribbon cable attached, which is terminated with an RJ11 6P4C connector. 

## Amplifier Design

The job of this amplifier is to amplify the Wheatstone bridge's signal up to a voltage level which will be compatible with the Arterial-BP-MRI-Triggering-Unit's analog input, which has a 0-5V range. I chose to place the amplifier inside the scan room, at the end of the scan table, to avoid the considerable challenge of manufacturing a quality of sufficient quality of drive and sense the bridge voltages inside the hostile environment of the MRI scanner.

The amplifier IC chosen was the Texas instruments INA125P. This amplifier combines a precision voltage reference for driving the bridge with a high quality instrumentation amplifier.

The chosen power supply for this circuit was a 9V battery, because several 9V alkaline batteries have been used inside the MRI scanners at the Toronto Center for Phenogenomics without ill effect. 

The gain was chosen to be 664 V/V, so that the entire 0 -- 300mmHg would be usable. Using the gain equation, this gave a gain resistor value of 90 ohms. 

This instrumentation amplifier's output voltage swing and linearity are limited to V+ - 1.7V and V- + 1V. To avoid performance degradation, I opted to set the output reference terminal to 2.5V, so that the amplifier would have sufficient headroom above and below the supply rails for all measurements within the transducer's usable range. The datasheet cautions that the reference terminal must be connected to a low impedance point, or the amplifier's common mode rejection would be degraded substantially. To avoid this, I placed a high quality voltage follower between the voltage divider and the reference terminal to ensure that the impedance was minimized.