## Motion Sensor with Particle Photon

Required Parts:
<br>- Particle Photon [link](https://www.amazon.com/Particle-Reprogrammable-Development-Prototyping-Electronics/dp/B016YNU1A0)
<br>- Adafruit PIR (Motion) Sensor [link](https://www.amazon.com/gp/product/B012ZZ4LPM/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1)
<br>- OPTIONAL: PowerShield (Battery) [link](https://www.amazon.com/gp/product/B06XJ64G8G/ref=ppx_yo_dt_b_asin_title_o02_s00?ie=UTF8&psc=1)
<br>- 3 Male/Female Wires (Orange, Yellow & Black)
<!---
[link](https://docs.particle.io/tutorials/hardware-projects/maker-kit/#tutorial-3-conference-room-monitor)
--->

### Step 1: SETUP WIFI & RESET PHOTON

<a href="https://iotrvc.github.io/reset/" target="blank">Click Here</a>


### Step 2: Connect Motion Sensor to Photon
- Connect your sensor to a digital or analog input on the Photon. Follow these examples for hooking up common sensors.
```
Sensor Pin | Photon Pin
       VCC | VIN 
       GND | GND 
       OUT | D0 
```
#### Set up the hardware

On the PIR sensor itself, with the back of the PCB facing up, find the two small potentiometers. The one on the left controls the range of the sensor (3-7 meters), and the one on the right controls how long the sensor stays triggered once tripped (1.5 - 300 seconds). 

Set the left (range potentiometer) to middle of dial. 

Set the right (timing potentiometer) all the way to the left (counterclockwise) to maximize its timing resolution.

<img src="6-1.jpg" width="500">
<br>
Now we'll connect the PIR sensor to the Photon. Press the sensor into the upper-left corner of your breadboard, then connect it to the Photon as follows:
<br>
<img src="6.png" width="500">

<hr>

### Step 3: Setup Push Notifications
To receive your push notification there is a little setup if you’ve never done it before, but once you do you’ll never have to touch it again. There are a ton of services you could use, but the one I like is Pushover.net. 

- Setup an account with <a href="https://pushover.net/" target="blank">Pushover.net</a> then follow these instructions:

- Follow below steps (see below image)

￼<img src="8.jpg" width="500">

￼<img src="7.jpg" width="500">

- Click on the Pushover logo to get to your dashboard.
- Copy your User Key. This is the value for the the user query field below.
- Add a device that is going to receive these push notifications. Primarily an iOS or Android phone/tablet… whatever you can download the Pushover app to.
- Send a test message and make sure it’s working!
- Register an application. Call it whatever you want it doesn’t really matter.
- When you are done click on the application’s name from step 5 and copy the API Token/Key from that page. This is the value for the token query field below.

<hr>

### Step 4: Create a WebHook

- Go to <a href="https://console.particle.io/integrations" target="blank">https://console.particle.io/integrations</a>
- Click on New Integration
- Change "Request Format" JSON
- Click WebHook > CUSTOM TEMPLATE
- Get code <a href="https://raw.githubusercontent.com/iotrvc/project1/master/trigger.txt" target="blank">here</a> and Copy and replace user
and token with above user key and API Token

<img src="triggers.png" width="500">


- Click Create WebHook

<hr>

### Step 5: Create Particle App

- Go to <a href="https://build.particle.io/build/new" target="blank">here</a>
- Title: Conference_Room_Monitor
- Paste Below Code

```cpp



/*****************************************************************************
This tutorial uses a Photon and the PIR motion sensor from the Particle Maker
Kit to determine whether a conference room is in use (you could also use it
for many other applications) and post the status to PushOver or IFTTT
******************************************************************************/

int ledPin = D7;                 // choose the pin for the LED
int inputPin = D0;               // choose the PIR sensor pin
bool available = false;          // status of conference room
int motionCounter = 0;           // variable to count motion events
bool loadfirst = false;

Timer timer(30000, determineMotion); // software timer to check every 30 seconds

void setup() {
  pinMode(ledPin, OUTPUT);       // set LED as output
  pinMode(inputPin, INPUT);      // set sensor as input
  determineMotion();
  timer.start(); // start the determineMotion timer
}

void determineMotion() {    // this function determines if there's motion
    if(motionCounter < 1) { // if very little motion was detected
        if(loadfirst == false){
             loadfirst = true;
             motionCounter = 0;
             Particle.publish("conference", "Confererence Room A Monitor Starting", PRIVATE);
             delay(1000); //stops double posting in IFTTT
        }
        else if(available == false) { // only publish if the status changed
             motionCounter = 0;
             Particle.publish("conference", "Confererence Room A is Available", PRIVATE);
             delay(1000); //stops double posting in IFTTT
            }
        available = true; // set the status to available
    } else if (motionCounter >= 1) {
        if(available == true) { // only publish if the status changed
            loadfirst = true;
            Particle.publish("conference", "Confererence Room A is In Use", PRIVATE);
             delay(1000); //stops double posting in IFTTT
            }
        available = false; // set the status to in use
         motionCounter = 0;
    }
}

void loop() {
  if (digitalRead(inputPin) == HIGH) {  // check if the input is HIGH
    digitalWrite(ledPin, HIGH);         // turn LED ON if high
    motionCounter++;                    // increment motion counter
  } else {
    digitalWrite(ledPin, LOW);          // turn LED OFF if no input
  }
  delay(500);                           // wait 0.5s
}



```

#### Conference_Room_Monitor
Or Get Shareable Code: <a href="https://go.particle.io/shared_apps/5e9b80cd3086540016b952bd" target="blank">Click Here</a>

- Click Save
- Click Flash


That’s It! You should now see notifications via the PushOver App when motion is detected

If all goes well, the D7 LED on your Photon should light up when the sensor detects motion. Note that it stays tripped for a second or two, so it may appear to be sluggish. To make sure it's actually working, hold perfectly still for a few seconds, then move. You'll find that it's sensitive enough to detect very subtle motion, which is great.


### TROUBLESHOOTING STEPS
<a href="https://iotrvc.github.io/troubleshooting/" target="blank">Click Here For TroubleShooting Steps</a>


