---
layout: post
title:  "Arduino &  MQ2 MQ3 MQ9 氣體感測器"
date:   2019-11-27 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Arduino & MQ2 MQ3 MQ9 

## 1. MQ2 MQ3 MQ9 接上插上 Arduino

![]({{ "images/2019-11-15/2019-11-18.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-15.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
```cpp

#include <MQUnifiedsensor.h>

#define pin A0 
#define type 2 //MQ2

//Declare Sensor
MQUnifiedsensor MQ2(pin, type);

//Variables
float H2, LPG, CO, Alcohol, Propane, Benzene;

void setup() {
  Serial.begin(9600); //Init serial port

  MQ2.inicializar(); 
}

void loop() {
  MQ2.update(); // Update data, the arduino will be read the voltaje in the analog pin
 
  H2 =  MQ2.readSensor("H2"); // Return H2 concentration
  LPG =  MQ2.readSensor("LPG"); // Return LPG concentration
  CO =  MQ2.readSensor("CO"); // Return CO concentration
  Alcohol =  MQ2.readSensor("Alcohol"); // Return Alcohol concentration
  Propane =  MQ2.readSensor("Propane"); // Return Propane concentration
  
  Serial.print("Volt: ");Serial.print(MQ2.getVoltage(false));Serial.println(" V"); 
  Serial.print("R0: ");Serial.print(MQ2.getR0());Serial.println(" Ohm"); 
  Serial.print("H2: ");Serial.print(H2,2);Serial.println(" ppm");
  Serial.print("LPG: ");Serial.print(LPG,2);Serial.println(" ppm");
  Serial.print("CO: ");Serial.print(CO,2);Serial.println(" ppm");
  Serial.print("Alcohol: ");Serial.print(Alcohol,2);Serial.println(" ppm");
  Serial.print("Propane: ");Serial.print(Propane,2);Serial.println(" ppm");
}
```

## 4. 結果

![]({{ "images/2019-11-15/IMG_0529.JPG" | relative_url }})   
