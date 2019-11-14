---
layout: post
title:  "Raspberry 讀取 DHT22 溫度感測器"
date:   2019-11-14 18:15:56 +0800
categories: RaspberryPi DHT22 
---

# DHT 22 資料的讀取

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})
2. 安裝 adafruit-blinka adafruit-circuitpython-dht 
```bash
pip3 install  adafruit-blinka adafruit-circuitpython-dht
```

## 2. 把 DHT 22 模組接上 樹莓派

| 接腳 | GPIO |
| --- | ---- |
| +   | 5V |
| out | GPIO17(pin 11) |
| -   | GND |

## 3. 程式碼
```python
import board
import time
import adafruit_dht

pin = board.D17 # GPIO 17
dhtDevice = adafruit_dht.DHT22(pin)

while True:
    try:
        temperature = dhtDevice.temperature
        humidity = dhtDevice.humidity

        print(f"Temperature: {temperature}, Humidity: {humidity}")

    except RuntimeError as error:
        print(error.args[0])

    time.sleep(2.0)
```

## 4. 結果
![]({{ "images/2019-11-14/螢幕快照_2019-11-14_18-43-19.png" | relative_url }})   

