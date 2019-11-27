---
layout: post
title:  "Arduino &  MQ2 MQ3 MQ9 氣體感測器"
date:   2019-11-27 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Arduino & MQ2 MQ3 MQ9 

## 1. MQ2 MQ3 MQ9 接上插上 Arduino

![]({{ "images/2019-11-27/2019-11-27_Arduino_MQ2_MQ3_MQ9_bb.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-27_Arduino_MQ2_MQ3_MQ9.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 2. 安裝函式庫
* MQUnifiedsensor

![]({{ "images/2019-11-27/螢幕快照_2019-11-27_14-21-18.png" | relative_url }})

## 3. 程式碼
```cpp
#include <MQUnifiedsensor.h>

MQUnifiedsensor MQ2(A3, 2); // pin A3
MQUnifiedsensor MQ3(A2, 3); // pin A2
MQUnifiedsensor MQ9(A1, 9); // pin A1
//Variables
float H2, LPG, CO, Alcohol, Propane, Benzene, Benzine, CH4, Hexane;


void setup() {
  Serial.begin(9600); //Init serial port

  MQ2.inicializar(); 
  MQ3.inicializar(); 
  MQ9.inicializar(); 
}

void loop() {
  MQ2.update(); 
  MQ3.update(); 
  MQ9.update(); 
 
  H2 =  MQ2.readSensor("H2"); 
  LPG =  MQ2.readSensor("LPG");
  CO =  MQ2.readSensor("CO"); 
  Alcohol =  MQ2.readSensor("Alcohol"); 
  Propane =  MQ2.readSensor("Propane");
  
  Serial.println("Lectures for MQ-2");
  Serial.print("Volt: ");Serial.print(MQ2.getVoltage(false));Serial.println(" V"); 
  Serial.print("R0: ");Serial.print(MQ2.getR0());Serial.println(" Ohm"); 
  Serial.print("H2: ");Serial.print(H2,2);Serial.println(" ppm");
  Serial.print("LPG: ");Serial.print(LPG,2);Serial.println(" ppm");
  Serial.print("CO: ");Serial.print(CO,2);Serial.println(" ppm");
  Serial.print("Alcohol: ");Serial.print(Alcohol,2);Serial.println(" ppm");
  Serial.print("Propane: ");Serial.print(Propane,2);Serial.println(" ppm");

  Serial.println("=====================");
  
  CH4 =  MQ3.readSensor("CH4");
  LPG =  MQ3.readSensor("LPG"); 
  CO =  MQ3.readSensor("CO"); 
  Alcohol =  MQ3.readSensor("Alcohol");
  Hexane =  MQ3.readSensor("Hexane");
  Benzine =  MQ3.readSensor("Benzene");
  Serial.println("Lectures for MQ-3");
  Serial.print("Volt: ");Serial.print(MQ3.getVoltage(false));Serial.println(" V"); 
  Serial.print("R0: ");Serial.print(MQ3.getR0());Serial.println(" Ohm"); 
  Serial.print("CH4: ");Serial.print(CH4,2);Serial.println(" mg/L");
  Serial.print("LPG: ");Serial.print(LPG,2);Serial.println(" mg/L");
  Serial.print("CO: ");Serial.print(CO,2);Serial.println(" mg/L");
  Serial.print("Alcohol: ");Serial.print(Alcohol,2);Serial.println(" mg/L");
  Serial.print("Hexane: ");Serial.print(Hexane,2);Serial.println(" mg/L");
  Serial.print("Benzine: ");Serial.print(Benzine,2);Serial.println(" mg/L");
  
  Serial.println("=====================");
  LPG =  MQ9.readSensor("LPG"); 
  CH4 =  MQ9.readSensor("CH4");
  CO =  MQ9.readSensor("CO"); 
  
  Serial.println("Lectures for MQ-9");
  Serial.print("Volt: ");Serial.print(MQ9.getVoltage(false));Serial.println(" V"); 
  Serial.print("R0: ");Serial.print(MQ9.getR0());Serial.println(" Ohm"); 
  Serial.print("CO: ");Serial.print(CO,2);Serial.println(" ppm");
  Serial.print("LPG: ");Serial.print(LPG,2);Serial.println(" ppm");
  Serial.print("CH4: ");Serial.print(CH4,2);Serial.println(" ppm");
  Serial.println("=====================");
  
  delay(2000);
}
```

## 4. 結果

![]({{ "images/2019-11-27/IMG_0659.JPG" | relative_url }})   
![]({{ "images/2019-11-27/螢幕快照_2019-11-27_14-48-19.png" | relative_url }})   
