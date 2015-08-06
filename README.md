# CPPM Library for Arduino

2015-08-06: Update in preparation...

This library provides an interface for reading a CPPM signal delivered by a RC receiver like Orange R615X.
Commonly, the 22ms frame limits the maximum number of servos/channels to 9.

### Example

    #include <CPPM.h>
    
    // your code...
    
    void setup(void)
    {
      CPPM.begin();
    }

    void loop(void)
    {
      if (CPPM.synchronized())
      {
        int throttle = CPPM.read_us(CPPM.THRO);
        
        // do something funny...
      }
    }

### 22ms CPPM Frame

![22ms CPPM Frame](http://jean-marc.paratte.ch/wp-content/uploads/2015/08/NewFile1.jpg)
```
22ms CPPM Frame
Note 1: CPPM frame is the concatenation of 9 servo pulses and 1 gap pulse (Orange R920X receiver).
Note 2: The T trig is set on the 1st falling edge after gap pulse (20ms left deviation).
```

![CPPM Frame Length](http://jean-marc.paratte.ch/wp-content/uploads/2015/08/NewFile0.jpg)
```
CPPM Frame Length
Note 1: Cursor A is set on falling edge of CPPM frame (1st sync pulse, 1st CPPM pulse).
Note 2: Cursor B is set on falling edge of next CPPM frame.
Note 3: CPPM frame length is more precisely 21980us (Spektrum DX8 emitter - Orange R920X receiver).
Note 4: Unsynchronized CPPM frame length is about 22120us (Orange R920X receiver).
Note 5: Unsynchronized CPPM frame length can largely vary from one model/manufacter to another.
```

### 1520us CPPM Pulse

![1520us CPPM Pulse](http://jean-marc.paratte.ch/wp-content/uploads/2015/08/NewFile3.jpg)
```
1520us CPPM Pulse
Note 1: Cursor A is set on falling edge of sync pulse.
Note 2: Cursor B is set on falling edge of next sync pulse.
Note 3: Middle CPPM pulse width is more precisely 1510us (Orange R920X receiver).
Note 4: Middle CPPM pulse width can largely vary from one model/manufacter to another.
Note 5: +/-100% CPPM range pulse width is about +/-455us (Orange R920X receiver).
Note 6: CPPM range pulse width can largely vary from one model/manufacter to another.
```

### CPPM Sync Pulse

![CPPM Sync Pulse](http://jean-marc.paratte.ch/wp-content/uploads/2015/08/NewFile5.jpg)
```
CPPM Sync Pulse
Note 1: Cursor A is set on falling edge of sync pulse.
Note 2: Cursor B is set on rising edge of sync pulse.
Note 3: CPPM sync pulse width is more precisely 136us (Orange R920X receiver).
Note 4: CPPM sync pulse width can largely vary from one model/manufacter to another.
```

### Implementation

This library works only on ATmega328 implementation of Arduino UNO, Duemillanove and similar, also Leonardo. It uses the 16bit Timer1. So PWM `analogWrite()` on pin 9 and 10 are unavailable. The library `Servo` is not compatible because it also uses Timer1.

The CPPM input signal is connected to the Arduino pin 8 named ICP1. A CPPM output signal is generated on pin 9 OC1A. At moment, pin 10 OC1B has no dedicated usage. 

This implementation differs from https://github.com/claymation/CPPM in one significant point: Edges of pulses are exactly catched by hardware counter ICR1 with a precision of 0.5 microsecond. TCNT1 counter is a true running counter and is never resetted by software.  

Implementation has been extensively tested with Orange R920X (9 channels) and Orange R615X (6 channels) receiver and a Spektrum DX8 transmitter. 

Protect the CPPM input signal from glitches that can catch ICP1. Add a 1nF capacitor between ICP1 and GND and insert a 330ohms resistor in the CPPM signal. This is particulary necessary when high currents are switched (dc motors, etc...).

The main page is http://jean-marc.paratte.ch/articles/arduino-cppm/ (French language).

### Installation

1. Download the ZIP file.
2. Use the Arduino menu Sketch / Import Library / Add Library...
3. Verify the expanded content in the folder Documents/Arduino/libraries/CPPM. 
4. Restart the Arduino environment.
5. Connect a receiver and try the Monitor example.

