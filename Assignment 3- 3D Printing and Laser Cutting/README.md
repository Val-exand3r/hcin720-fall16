<h1>IA3- designing for 3D printing and laser cutting</h1>

<h2>Introduction</h2>
<p>The goals of this assignment were to learn how to design for both 3D Printing, and Laser Cutting, and also learn how to use both the 3D Printing equipment, and the Laser Cutter</p> 

<h2>Equipments Used</h2>
<ol>
  <li>3D Printing</li>
    <ul>
      <li>Device- LultBolt Mini</li>
      <li>Software- <ul><li><a href="https://www.tinkercad.com/" target="_blank">TinkerCad</a></li>
                        <li><a href="https://www.lulzbot.com/cura" target="_blank">Cura</a></li>
                    </ul></li>
     </ul>
  <li>Laser Cutting</li>
  <ul>
      <li>Universal Laser System</li>
      <li>Software- Adobe Illustrator</li>
  </ul>
</ol>

<h2>3D Printed Case- Speedometer </h2>
![][3d]
<p>Figure 1:3D printed box</p>
[3d]:https://github.com/Val-exand3r/hcin720-fall16/raw/master/Assignment2-%252520Senors/photonJavascript/jquery.animateSprite/jquery.animateSprite-master/img/project2setup.jpg

<p>It was build to look like a speedometer. Enclosed in a Flora LSM 9DOF Motion Sensor. It is used to sense changes in motion, and sends a signal to 2 output  devices (Neopixel Strips, and Servo motor.</p><br>
<p>When a motion signal is received, the Neopixel strip, displays a rainbow color, and at the same time, the hand of a 3D printed clock, placed on the servo motor also moves.</p>



<h2>Laser Case- CLOCK</h2>
![][laser]
<p>Figure 1:3D printed box</p>
[laser]:h

<p>It was built to look like a clock with gears around it. the entire case was built with wood. It also has a Neopixel strip and Servo motor attached to it. and has the same function as the  3d printed case above</p>

<h2>New Photon code to include the Servo motor functionality</h2>

```c
// This #include statement was automatically added by the Particle IDE.
#include "neopixel/neopixel.h"

// This #include statement was automatically added by the Particle IDE.
#include "Adafruit_LSM9DS0/Adafruit_LSM9DS0.h"

//#include "Adafruit_LSM9DS0/Adafruit_LSM9DS0.h"
#include "Adafruit_LSM9DS0/Adafruit_Sensor.h"  // not used in this demo but required!
#include <math.h>
// i2c
//Adafruit_LSM9DS0 lsm = Adafruit_LSM9DS0();


// mess with this number to adjust TWINklitude :)
// lower number = more sensitive
#define MOVE_THRESHOLD 200

/* Assign a unique base ID for this sensor */
Adafruit_LSM9DS0 lsm = Adafruit_LSM9DS0(1000);  // Use I2C, ID #1000
// You can also use software SPI
//Adafruit_LSM9DS0 lsm = Adafruit_LSM9DS0(13, 12, 11, 10, 9);
// Or hardware SPI! In this case, only CS pins are passed in
//Adafruit_LSM9DS0 lsm = Adafruit_LSM9DS0(10, 9);

#define  Output_Pin D3 //for the LED OUTPUT BECAUSE IT IS CONSTANT
 
void rainbowCycle(uint8_t wait);
uint32_t Wheel(byte WheelPos);

Servo myservo; 
int servoPos = 0;

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
// char publishAcceleration[128];
// char publishGyroscope[128];
// char publishData[128];
int i=0;




void setup()
{
  //while (!Serial); // flora & leonardo
    
    myservo.attach(D2);   // attach the servo on the D0 pin to the servo object
   myservo.write(5);

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
  
  Particle.subscribe("motionChange", senseMotion);
  setupSensor();
  
   //subscribe to function
   //Particle.subscribe("motionChange", senseMotion);
 //  Particle.subscribe("Acceleration", send9dof);
   // Particle.subscribe("Gyroscope", send9dofGry);
}

 void senseMotion(const char *event, const char *data)
{
     // are we moving 
   // int da =(int)data;
   
    // char data2[11];
    // strcpy(data2, data);
      rainbowCycle(1);
     float x = atof(data);
    // float use = float(x);
    // int dd =300;
    //   Serial.println(x);
   // colorWipe(strip.Color(205, 0, 0), 5);
         //colorWipe(strip.Color(0, 0, 0), 5); 
//   if ( x > 5000) {
// //   // Serial.println("Twinkle!");
// //   // Serial.println(data);
// //     // colorWipe(strip.Color(255, 0, 0), 25);
//         colorWipe(strip.Color(0, 255, 255), 5);
// //       colorWipe(strip.Color(0, 0, 0), 25);
//         Serial.print("fast- "); Serial.println(data);
// //     }else{
    
       
        
//          //colorWipe(strip.Color(205, 0, 0), 5);
//          colorWipe(strip.Color(0, 0, 0), 5);  
     
      
       
//     }else{
//          Serial.print("slow- "); Serial.println(data);
//           colorWipe(strip.Color(255, 0, 0), 5);
//          colorWipe(strip.Color(0, 0, 0), 5); 
//     }
}

 void send9dof(const char *event, const char *data)
{
    Serial.println("Acce");
    Serial.println(data);
    char data2[50];
    strcpy(data2, data);
    // char *pointer;
    // pointer = strtok(data2, ":")
    
}

 void send9dofGry(const char *event, const char *data)
{
    Serial.println("Gyro");
    Serial.println(data);
    char data2[50];
    strcpy(data2, data);
    // char *pointer;
    // pointer = strtok(data2, ":")
    
}



void loop()
{
     //Particle.publish("temp", temp, PRIVATE);
    // Take a reading of accellerometer data
   //String accValues = "Acceleromoter";
  
   
   
//     Serial.print("Accel X: "); Serial.print(accel.acceleration.x); Serial.print(" ");
//   Serial.print("  \tY: "); Serial.print(accel.acceleration.y);       Serial.print(" ");
//   Serial.print("  \tZ: "); Serial.print(accel.acceleration.z);     Serial.println("  \tm/s^2");

//   // print out magnetometer data
//   Serial.print("Magn. X: "); Serial.print(mag.magnetic.x); Serial.print(" ");
//   Serial.print("  \tY: "); Serial.print(mag.magnetic.y);       Serial.print(" ");
//   Serial.print("  \tZ: "); Serial.print(mag.magnetic.z);     Serial.println("  \tgauss");

//   // print out gyroscopic data
//   Serial.print("Gyro  X: "); Serial.print(gyro.gyro.x); Serial.print(" ");
//   Serial.print("  \tY: "); Serial.print(gyro.gyro.y);       Serial.print(" ");
//   Serial.print("  \tZ: "); Serial.print(gyro.gyro.z);     Serial.println("  \tdps");

//   // print out temperature data
//   Serial.print("Temp: "); Serial.print(temp.temperature); Serial.println(" *C");

   
    
  lsm.read();
//   Serial.print("Accel X: "); Serial.print(lsm.accelData.x); Serial.print(" ");
//   Serial.print("Y: "); Serial.print(lsm.accelData.y);       Serial.print(" ");
//   Serial.print("Z: "); Serial.print(lsm.accelData.z);     Serial.print(" ");
 
  // Get the magnitude (length) of the 3 axis vector
  // http://en.wikipedia.org/wiki/Euclidean_vector#Length
    
  
  double storedVector = lsm.accelData.x*lsm.accelData.x;
  storedVector += lsm.accelData.y*lsm.accelData.y;
  storedVector += lsm.accelData.z*lsm.accelData.z;
  storedVector = sqrt(storedVector);
 // Serial.print("Len: "); Serial.println(storedVector);
//   Serial.print("first: "); 
//   Serial.println(storedVector);
   
   //Particle.publish("inputValue", data, PRIVATE);
  // wait a bit
  
 
  
  //Publish first Accelere value
  // Particle.publish("acce1", data);

  
  delay(100);
  
  // get new data!
  lsm.read();
  double newVector = lsm.accelData.x*lsm.accelData.x;
  newVector += lsm.accelData.y*lsm.accelData.y;
  newVector += lsm.accelData.z*lsm.accelData.z;
  newVector = sqrt(newVector);
//   Serial.print("second: "); 
//   Serial.println(newVector);
 
    
   //String data = String(storedVector);
 //Publish first Accelere value
  // Particle.publish("acce2", data);
    ///String data = String(abs(newVector - storedVector) );
    //Particle.publish("motionChange", data);
  // are we moving 
  
//   String accX = strcat(String(lsm.accelData.x), ",");
//   String accY = strcat(String(lsm.accelData.y), ",");  
 //  String accZ = String(lsm.accelData.z);   
 //   String all = strcat(accX, accY, accZ);    
    double sending =abs(newVector - storedVector);
  
  if ( sending > MOVE_THRESHOLD) {
      
       myservo.write(25);
             
     
    //Serial.println("Twinkle!");
   // double sending =abs(newVector - storedVector);
    
    //String data = String(abs(newVector - storedVector) );
    //Serial.println(abs(newVector - storedVector));
    //Particle.publish("motionChange", data);
    //double sending =abs(newVector - storedVector);
    
    // sprintf(publishData,"%d", abs(newVector - storedVector));
    // Particle.publish("motionChange", publishData);
   
    // sprintf(publishAcceleration,"%.2f,%.2f,%.2f",lsm.accelData.x,lsm.accelData.x,lsm.accelData.
    // z);
    // Particle.publish("Acceleration", publishAcceleration);
    
    // sprintf(publishGyroscope,"%.2f,%.2f,%.2f",lsm.gyroData.x,lsm.gyroData.x,lsm.gyroData.
    // z);
    // Particle.publish("Gyroscope", publishGyroscope);
   // double sending =(newVector - storedVector);
   // if(sending>0)
   // { 
        //String data = String(abs(newVector - storedVector) ) ;
     Particle.publish("motionChange", String(sending)  );
     delay(100);
       myservo.write(180);
       
       delay(100);
       myservo.write(0);
     //publish(String(sending));
     //Particle.publish("motionChange", String(abs(newVector - storedVector) ) );
     //Particle.publish("motionChange",String(newVector));
   
    //  Serial.print("Accel X: "); Serial.print(lsm.accelData.x); Serial.print(" ");
    //   Serial.print("Y: "); Serial.print(lsm.accelData.y);       Serial.print(" ");
    //   Serial.print("Z: "); Serial.print(lsm.accelData.z);     Serial.print(" ");
    // //} 
    
    
    //colorWipe(strip.Color(255, 0, 0), 25);
    //colorWipe(strip.Color(0, 255, 255), 25);
   // colorWipe(strip.Color(0, 0, 0), 25);
    
   }else{
       colorWipe(strip.Color(0, 0, 0), 5);
       
   }
  
  
}
 

// void publish(String text){
//     Particle.publish("motionChange", text  );
    
// }
 
  void colorWipe(uint32_t c, uint8_t wait) {
      for(uint16_t i=0; i<strip.numPixels(); i++) {
          strip.setPixelColor(i, c);
          strip.show();
          delay(wait);
      }
  }
  
  void rainbowCycle(uint8_t wait) {
  uint16_t i, j;

  for(j=0; j<256; j++) { // 1 cycle of all colors on wheel
    for(i=0; i< strip.numPixels(); i++) {
      strip.setPixelColor(i, Wheel(((i * 256 / strip.numPixels()) + j) & 255));
    }
    strip.show();
    delay(wait);
  }
}

// Input a value 0 to 255 to get a color value.
// The colours are a transition r - g - b - back to r.
uint32_t Wheel(byte WheelPos) {
  if(WheelPos < 85) {
   return strip.Color(WheelPos * 3, 255 - WheelPos * 3, 0);
  } else if(WheelPos < 170) {
   WheelPos -= 85;
   return strip.Color(255 - WheelPos * 3, 0, WheelPos * 3);
  } else {
   WheelPos -= 170;
   return strip.Color(0, WheelPos * 3, 255 - WheelPos * 3);
  }
}
  





```
