---
layout: post
title:  "Raspberry & Arduino Lora 傳輸"
date:   2019-11-29 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Raspberry & Arduino Lora 傳輸
其實跟昨天一樣，只是接收的換成了樹莓派...   
其實可以反過來傳輸，程式碼接收改傳送，傳送改接收就好

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})

## 2. 將Lora USB傳輸接上插上樹莓派
USB 插上去還要圖嗎？ 

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

> Arduino
請看昨天的程式碼

## 4. 結果

![]({{ "images/2019-11-29/螢幕快照_2019-11-29_15-40-08.png" | relative_url }})   


備註，我偷懶一天，明天要早起 Q—Q   
