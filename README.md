## Motion Sensor with Particle Photon

Required Parts:
Particle Photon Development Kit
Adafruit PIR (Motion) Sensor

### Step 1: Set up your Photon
- Using Your Computer: Go to particle.io/setup and follow the instructions to create an account and set up your Photon.
- Using Your Phone & Download the Particle Mobile App [iPhone](https://itunes.apple.com/us/app/particle-build-photon-electron/id991459054?ls=1&mt=8) & [Android](https://play.google.com/store/apps/details?id=io.particle.android.app) to create an account and set up your Photon.

<img src="9.jpg" width="500">

<hr>

### Step 2: Connect Motion Sensor to Photon
- Connect your sensor to a digital or analog input on the Photon. Follow these examples for hooking up common sensors.
```
Sensor Pin | Photon Pin
       VCC | VIN (RED)
       GND | GND (BLUE)
       OUT | D0 (YELLOW)
```
<img src="6.jpg" width="500">

<hr>

### Step 3: Setup Push Notifications
To receive your push notification there is a little setup if you’ve never done it before, but once you do you’ll never have to touch it again. There are a ton of services you could use, but the one I like is Pushover.net. 

- Setup an account with [Pushover.net](https://pushover.net/) then follow these instructions:
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

- Go to https://console.particle.io/integrations 
- Click on New Integration
- Click WebHook > CUSTOM TEMPLATE
- Paste below text and replace user and token with above user key and API Token
```
{
  "eventName": "office-motion",
  "url": "https://api.pushover.net/1/messages.json",
  "requestType": "POST",
  "query":
  {
    "user": "YOUR_USER_KEY",
    "token": "YOUR_API_TOKEN_KEY",
    "title": "MOTION",
    "message": "{{SPARK_EVENT_VALUE}}"
  },
  "mydevices": true
}
```

- Click Create WebHook

<hr>

### Step 5: Create Particle App

- Go to https://build.particle.io/build/new 
- Title: MotionHook
- Paste Below Code
```
#define PIR_PIN D0 // Replace D0 with the pin you used 
void setup() { 
   pinMode(PIR_PIN, INPUT); 
} 
void loop() { 
   // PIR_PIN goes HIGH when motion is detected, stays HIGH for a few seconds 
 if (digitalRead(D0) == HIGH) {
        Particle.publish("office-motion", "DiningRoom", 60, PRIVATE);
        while (digitalRead(D0) == HIGH); // hang tight here until motion stops
    }
} 
```
- Click Save
- Click Flash

That’s It! You should now see notifications when motion is detected


