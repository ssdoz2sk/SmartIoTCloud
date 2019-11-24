---
layout: post
title:  "Arduino & 溫度感測器"
date:   2019-11-24 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Arduino & 溫度感測器

## 1. 將溫度感測器接上插上Arduino

![]({{ "images/2019-11-24/2019-11-24_Arduino_DHT22_bb.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-24_Arduino_DHT22.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
```cpp
#include "DHT.h"
#define DHTPIN 2   
#define DHTTYPE DHT22  

DHT dht(DHTPIN, DHTTYPE); // Initialize DHT sensor

void setup() {
  Serial.begin(9600);
  dht.begin();
}

void loop() {
  delay(2000);
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  Serial.print("Humidity: ");
  Serial.print(h);
  Serial.println("%");
  Serial.print("Temperature: ");
  Serial.print(t);
  Serial.println("*C");
}
```

## 4. 結果

![]({{ "images/2019-11-24/2019-11-24.PNG" | relative_url }})   
![]({{ "images/2019-11-24/IMG_0640.JPG" | relative_url }})   
![]({{ "images/2019-11-24/IMG_0641.JPG" | relative_url }})   