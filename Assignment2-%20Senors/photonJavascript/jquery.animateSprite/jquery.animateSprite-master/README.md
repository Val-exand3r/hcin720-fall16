<h1>IA2- Sending & Reading Data to/from the Cloud</h1>

<h2>Introduction</h2>
<p>The main goal for this assignment was to send data back and forth from a physical object (the Photon) to a computer. 

"This was done via the Particle cloud.</p>

<p>Input Device- Flora LSM 9DOF Motion Sensor</p>

<p>Output Device- NeoPixel Strips</p>

<h2>Input Visualization</h2>
<p>For visualizing the Accelerometer reading from the input device, I used the concept of Euclidean Vectors. The Vector is essentially what is needed to move an object from point A to point B, and the magnitude of a vector is the distance between the two points <cite>
<a href="https://en.wikipedia.org/wiki/Euclidean_vector" target="_blank">Wikipedia</a></cite> </p>

<p> Using my Input device, I am sensing the x, y and Z co-ordinates from the Accelerometer,and calculating the magnitude at the first point. I then read in a second x, y, z Accelerometer values and calculate the magnitude as well. If the difference in the magnitude at point A and B is greater than my set Threshold value, it means that a change has occured, thus, motion has begun.</p>

<h3>Photon code </h3>
<p>Main code adopted from Adafruit.com, and customized by  me</p>
<p>This reads Inputs from the sensor, publishing it to the cloud. The output device (Neopixel strips) subscribes to the data, and displays accordingly</p>



```c
//This #include statement was automatically added by the Particle IDE.
#include "neopixel/neopixel.h"

// This #include statement was automatically added by the Particle IDE.
#include "Adafruit_LSM9DS0/Adafruit_LSM9DS0.h"

//#include "Adafruit_LSM9DS0/Adafruit_LSM9DS0.h"
#include "Adafruit_LSM9DS0/Adafruit_Sensor.h"  // not used in this demo but required!
#include "math.h"



// mess with this number to adjust TWINklitude :)
// lower number = more sensitive
#define MOVE_THRESHOLD 200

/* Assign a unique base ID for this sensor */
Adafruit_LSM9DS0 lsm = Adafruit_LSM9DS0(1000);  // Use I2C, ID #1000


#define  Output_Pin D3 //for the LED OUTPUT BECAUSE IT IS CONSTANT
    

void setupSensor()
{
  // 1.) Set the accelerometer range
  lsm.setupAccel(lsm.LSM9DS0_ACCELRANGE_2G);
  //lsm.setupAccel(lsm.LSM9DS0_ACCELRANGE_4G);
  //lsm.setupAccel(lsm.LSM9DS0_ACCELRANGE_6G);
  //lsm.setupAccel(lsm.LSM9DS0_ACCELRANGE_8G);
  //lsm.setupAccel(lsm.LSM9DS0_ACCELRANGE_16G);

  // 2.) Set the magnetometer sensitivity
  lsm.setupMag(lsm.LSM9DS0_MAGGAIN_2GAUSS);
  //lsm.setupMag(lsm.LSM9DS0_MAGGAIN_4GAUSS);
  //lsm.setupMag(lsm.LSM9DS0_MAGGAIN_8GAUSS);
  //lsm.setupMag(lsm.LSM9DS0_MAGGAIN_12GAUSS);

  // 3.) Setup the gyroscope
  lsm.setupGyro(lsm.LSM9DS0_GYROSCALE_245DPS);
  //lsm.setupGyro(lsm.LSM9DS0_GYROSCALE_500DPS);
  //lsm.setupGyro(lsm.LSM9DS0_GYROSCALE_2000DPS);
}

Adafruit_NeoPixel strip = Adafruit_NeoPixel(20, Output_Pin); 

int i=0;


void setup()
{
   
   Serial.begin(9600);
   Serial.println("LSM raw read demo");
   pinMode(Output_Pin,OUTPUT);
   strip.begin();
   strip.show(); // Initialize all pixels to 'off'

  // Try to initialise and warn if we couldn't detect the chip
  if (!lsm.begin())
  {
    Serial.println("Oops ... unable to initialize the LSM9DS0. Check your wiring!");
    while (1);
  }
  Serial.println("Found LSM9DS0 9DOF");
  Serial.println("");
  Serial.println("");
  //
  Particle.subscribe("motionChange", senseMotion);
  setupSensor();
  
}

 void senseMotion(const char *event, const char *data)
{
     float x = atof(data);
   if ( x > 5000) { //it is moving faster, blink blue

        colorWipe(strip.Color(0, 255, 255), 5);
        colorWipe(strip.Color(0, 0, 0), 5);  
     
      
       
    }else{
         Serial.print("slow- "); Serial.println(data);
          colorWipe(strip.Color(255, 0, 0), 5);
         colorWipe(strip.Color(0, 0, 0), 5); 
    }
}




void loop()
{
  lsm.read();
// Get the magnitude (length) of the 3 axis vector
  // http://en.wikipedia.org/wiki/Euclidean_vector#Length
  
  
  double storedVector = lsm.accelData.x*lsm.accelData.x;
  storedVector += lsm.accelData.y*lsm.accelData.y;
  storedVector += lsm.accelData.z*lsm.accelData.z;
  storedVector = sqrt(storedVector);
 

  
  delay(100);
  
  // get new data!
  lsm.read();
  double newVector = lsm.accelData.x*lsm.accelData.x;
  newVector += lsm.accelData.y*lsm.accelData.y;
  newVector += lsm.accelData.z*lsm.accelData.z;
  newVector = sqrt(newVector);


  // are we moving 
  
    double sending =abs(newVector - storedVector);
  
  if ( sending > MOVE_THRESHOLD) {
   Particle.publish("motionChange", String(sending)  );
     delay(500);
        
   }
  
  
}

 
  void colorWipe(uint32_t c, uint8_t wait) {
      for(uint16_t i=0; istrip.numPixels(); i++) {
          strip.setPixelColor(i, c);
          strip.show();
          delay(wait);
      }
  }
  

```

<h3>Set-Up and Output Images</h3>
![][setup]
<p>Figure 1:Input and Output setup image, showing data being read from the cloud, and displayed on the Neopixels</p><br>
[setup]:https://github.com/Val-exand3r/hcin720-fall16/raw/master/Assignment2-%252520Senors/photonJavascript/jquery.animateSprite/jquery.animateSprite-master/img/project2setup.jpg

![][notmoving]
<p>Figure 2:Visualization on the webpage when no motion has occured</p><br>
[notmoving]:https://github.com/Val-exand3r/hcin720-fall16/raw/master/Assignment2-%252520Senors/photonJavascript/jquery.animateSprite/jquery.animateSprite-master/img/notmoving.png

![][moving]
<p>Figure 3:Visualization on the webpage when motion begins </p><br>
[moving]:https://github.com/Val-exand3r/hcin720-fall16/raw/master/Assignment2-%252520Senors/photonJavascript/jquery.animateSprite/jquery.animateSprite-master/img/moving.png

![][speed]
<p>Figure 4: Speedometer created with D3 framework, that keeps updating as the acceleration changes</p>
[speed]:https://github.com/Val-exand3r/hcin720-fall16/raw/master/Assignment2-%252520Senors/photonJavascript/jquery.animateSprite/jquery.animateSprite-master/img/speedometer.png

<h2>Extra Visualization from an external data source</h2>
<p>For extra points,  I consumed data from a live data stream, <a href="https://www.pubnub.com/" target="_blank">PUBNUB Tweets</a>. Once every 10 seconds, I post the country name from the tweet at that time, to the cloud. The Photon gets the country's name, and changes the color of the Neopixel ring , to match the color of the country's flag</p>
![][extra]
[extra]:https://github.com/Val-exand3r/hcin720-fall16/raw/master/Assignment2-%252520Senors/photonJavascript/jquery.animateSprite/jquery.animateSprite-master/img/extra.jpg
<p>Figure 5: Neopixel ring displaying the flag color of the country(France) that sent in a tweet</p>
<a href="http://rawgit.com/Val-exand3r/hcin720-fall16/master/Assignment2-%2520Senors/photonJavascript/jquery.animateSprite/jquery.animateSprite-master/walkingLive.html" target="_blank">
Input Visuation
</a><br><br>
<a href="http://rawgit.com/Val-exand3r/hcin720-fall16/master/Assignment2-%20Senors/photonJavascript/particleTweet.html" target="_blank">OutPut Visualization</a>
