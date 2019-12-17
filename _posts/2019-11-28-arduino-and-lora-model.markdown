---
layout: post
title:  "Arduino & LoRa 模組"
date:   2019-11-28 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Arduino & LoRa 模組

## 1. 將LoRa接上插上Arduino

![]({{ "images/2019-11-15/2019-11-18.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-15.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 2. 程式碼
```cpp
#include <SPI.h>
#include <LoRa.h>

int counter=0;  //傳送次數計數器

void setup() {
  Serial.begin(9600);
  while (!Serial);  //等待序列埠起始完畢
  Serial.println("LoRa Sender");
  if (!LoRa.begin(915000000)) {  //915MHz LoRa
    Serial.println("Starting LoRa failed!");
    while (1);
    }
  }

void loop() {
  Serial.print("Sending packet: ");
  Serial.println(counter);
  
  LoRa.beginPacket(); 
  LoRa.print("hello ");
  LoRa.print(counter); 
  LoRa.endPacket(); 
  
  counter++; 
  delay(5000);
}
```

## 3. 結果

![]({{ "images/2019-11-15/IMG_0529.JPG" | relative_url }})   
