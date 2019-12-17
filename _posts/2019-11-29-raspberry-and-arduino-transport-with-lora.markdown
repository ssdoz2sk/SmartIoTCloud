---
layout: post
title:  "Raspberry & Arduino Lora 傳輸"
date:   2019-11-29 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Raspberry & Arduino Lora 傳輸

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})

## 2. 將Lora 傳輸接上插上樹莓派

![]({{ "images/2019-11-15/2019-11-18.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-15.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
> raspberry
```python
import Lora
import time
import os
import serial
from Lora import CRCCheckError

def Fun_main():
    LoRa = Lora.LoRa()
    LoRa.reset()
    LoRa.readSetup()

    # 設定讀取和頻段
    LoRa.setRX()

    # 讀取資料
    c = 0
    while True:
        try:
            data = LoRa.read()
            print(data)
            len1 = len(data)
            
            if len1 > 4:
                HexString = LoRa.arrayToHexStringKeepPureData(data)
                print("hex: {}".format(HexString))
                print("UTF:{} {}".format(c, bytes.fromhex(HexString)))
            else:
                print("no data")
            c += 1
            
        except CRCCheckError as e:
            print(e)
        finally:
            time.sleep(1)


if __name__ == '__main__':
    Fun_main()
```
```

> Arduino
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
## 4. 結果

![]({{ "images/2019-11-15/IMG_0529.JPG" | relative_url }})   
