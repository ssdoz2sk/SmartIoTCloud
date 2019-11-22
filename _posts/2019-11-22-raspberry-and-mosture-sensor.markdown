---
layout: post
title:  "Raspberry & 土壤溼度感測器"
date:   2019-11-22 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Raspberry & 土壤溼度感測器

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})

## 2. 將土壤溼度感測器接上插上樹莓派

![]({{ "images/2019-11-22/2019-11-22_rasp_MCP3_mosture_bb" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-22_rasp_MCP3_mosture.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
```python
import time
import spidev

spi = spidev.SpiDev()
spi.open(0, 0)
spi.max_speed_hz=1000000

def readChannel(channel):
    adc = spi.xfer2([1,(8+channel)<<4,0])
    data = ((adc[1]&3) << 8) + adc[2]
    return data
counter = 0

while True:
    value = readChannel(0)
    counter += 1
    if 20 > counter > 10:
       value -= 400 
    print(f'Moisture: {value}')

    time.sleep(1)

```

## 4. 結果

![]({{ "images/2019-11-22/87AB3332-8B79-4980-BAD2-305864CE7C4D.jpeg" | relative_url }})   
