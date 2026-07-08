# Temperature-Regulator-
Matlab and simulink based
Arduino ON/OFF Temperature Controller (MATLAB/Simulink)

Group 18 project, CPUT control systems /process control module. Builds an on/off (bang-bang) temperature
controller, simulates it in Simulink, then deploys the same model to an Arduino Uno and
runs it against real hardware.

Control logic

Two thresholds:


Below 23°C: heater on, fan off.
Above 90°C: heater off, fan on.
Between the two: hold previous state. This is what stops the relay from switching on
every small fluctuation.


Temperature comes from an LM35 analog sensor, 10mV per °C, no offset. Conversion from
Arduino analog read (0-1023) to °C is the standard LM35 formula: (AnalogValue x 5.0 / 1023) x 100.

Simulink model

Built in this order:


Constant block for setpoint.
Pulse generator standing in for a live sensor signal during pure simulation.
Sum block to get error (setpoint minus measured temp).
Relay block driving heater output (0 or 255, PWM duty cycle).
Interval Test Dynamic block checking if temp is between setpoint and 100°C, driving fan
logic.
Gain block (x255) to scale the boolean fan signal to PWM range.
Scope blocks on error, temperature, fan output, heater output for verification before
touching hardware.


Once scopes confirmed correct switching behavior in simulation, hardware blocks were
added: Arduino analog input on pin A4, PWM output on pin 3 (heater), PWM output on pin 5
(fan), plus a 0.1 gain block converting the raw analog signal to °C. Model was then built
and deployed to the Uno directly from Simulink's Hardware tab, no separate Arduino sketch
involved.

Hardware


Arduino Uno
Relay module
LM35 sensor
12V DC bench supply
Heatsink with resistors wired to it, standing in for the heater load
2x cooling fans
MOSFETs to switch the heater/fan loads (Arduino pins can't drive them directly)
Screw terminals for supply and load connections


MOSFETs get a 5V signal from the Arduino and pass 12V through to the heater or fan
depending on which output is active.

Results

Fan output stayed high (ON) for effectively the entire test run. Error signal stayed
negative and did not trend toward zero, meaning measured temperature stayed above setpoint
the whole time. Temperature reading itself moved from about 33°C to just under 34°C and
then held flat, no real cooling response visible in the data.

Possible causes, not confirmed:


Fan/heatsink combination undersized for the heat load being simulated.
Controller gains not tuned (there's no tuning in an on/off controller in the classic
sense, but the threshold gap and hysteresis band weren't validated against actual thermal
response time).
Sensor reading offset or noise not fully corrected before deployment.


Simulation matched expected switching behavior. Hardware run did not show the temperature
settling near setpoint within the test window.
