---
layout: post
title:  "Arduino & LoRa 模組"
date:   2019-11-28 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Arduino & LoRa 模組傳輸
註記：此模組用特定廠商的 LoRa 模組，不僅不便宜，還不好用...，那個傳輸 byte 只能傳輸 20 長度的坑真的坑死人。   
考慮花時間重寫函式庫...

## 1. 將LoRa接上插上Arduino
![]({{ "images/2019-11-28/2019-11-28_Arduino_Lora_bb.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-28_Arduino_Lora.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

Library 在這裡：https://github.com/iFrogLab/IL-LORA1272/tree/master/Samples-Arduino/Ver2/iFrogLabLoRaLibrary

## 2. 程式碼
```cpp
#include <iFrogLabLoRaLibrary.h>
#include "DHT.h"

#define DHTPIN 2
#define DHTTYPE DHT22 

iFrogLabLoRaLibrary LoRa(10,11,9);  // RX, TX, DataReady
DHT dht(DHTPIN, DHTTYPE);

void setup()  {
  Serial.begin(9600);
  while (!Serial) {
        ; // wait for serial port to connect. Needed for native USB port only
  }
  
  byte TXRX=2;  // Mode : Sleep(0x00)、StandBy(0x01)、Tx(0x02)、Rx(0x03)。  
  // Freq : 輸入915.00MHz先轉成91500再轉16進位方式填入0x1656C -> 0x01 0x65 0x6C，SX1272範圍(860.00 ~ 1020.00MHz)，SX1276範圍(137.00 ~ 1020.00MHz)。
  byte Freq1=0x01;
  byte Freq2=0x65;
  byte Freq3=0x6c;
  byte Power =0x3;  // Power:125k(0x01)、250k(0x2)、500k(0x3)。Default 500K。 
  
  byte* data=LoRa.Setup(TXRX, Freq1, Freq2, Freq3, Power);
 
  
  Serial.print("Return:");
  Serial.print(data[0],HEX);
  Serial.print(data[1],HEX);
  Serial.print(data[2],HEX);
  Serial.print(data[3],HEX);
    
  Serial.println("");
  Serial.println("--------------------------");
  Serial.println("");
  
}

void loop()  {
  float h = dht.readHumidity();
  float t = dht.readTemperature();
  
  if (isnan(h) || isnan(t)) {
    Serial.println("Failed to read from DHT sensor!");
    delay(2000);
    return;
  }
  Serial.print("Humidity: ");
  Serial.print(h);
  Serial.print(" %\t");
  Serial.print("Temperature: ");
  Serial.println(t);

  String data = "Temp:" + String(t) + " Hum:" + String(h);
  Serial.println(data);
  char* d = data.c_str();
  // 函式庫有 bug , 字串不能太長，20個以上會crash
  LoRa.Write16bytesBroadcast(d, strlen(d));
  delay(2000);
}

```

## 3. 結果

![]({{ "images/2019-11-28/IMG_0661.JPG" | relative_url }})   
![]({{ "images/2019-11-28/螢幕快照_2019-12-17_18-22-46.png" | relative_url }})   
