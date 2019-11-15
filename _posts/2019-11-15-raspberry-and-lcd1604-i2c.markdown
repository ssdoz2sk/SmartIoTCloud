---
layout: post
title:  "Raspberry 寫入 LCD1604 顯示器"
date:   2019-11-15 18:09:11 +0800
categories: RaspberryPi LCD1604
---

# Raspberry 寫入 LCD1604 顯示器

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})
2. 安裝 i2c-tools （獲取 i2c資訊），RPLCD, smbus2
```bash
sudo apt install i2c-tools
pip3 install RPLCD smbus2
```

## 2. 把 LCD1604 模組接上 樹莓派

| 接腳 | GPIO |
| --- | ---- |
| GND | GND |
| Vcc | 5V |
| SDA | GPIO2 (pin3) |
| SCL | GPIO3 (pin5) |

--- 

![]({{ "images/2019-11-15/2019-11-15_LCD_1604_bb.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-15_LCD_1604.fzz" | relative_url }})   
[元件位置]({{ "files/fz_part/LCD I2C 1602.fzpz" | relative_url }})


## 3. 尋找 I2C 裝置的位址
```bash
i2cdetect -y 1
```
其中唯一非 `--` 的字串就是此裝置的位址

![]({{ "images/2019-11-15/螢幕快照_2019-11-15_21-06-01.png" | relative_url }})   

## 3. 程式碼
```python
import time
from RPLCD.i2c import CharLCD

lcd = CharLCD('PCF8574', address=0x27, port=1, backlight_enabled=True)
lcd.clear()

while True:
    try:
        lcd.cursor_pos = (0, 0)
        lcd.write_string(f"Date: {time.strftime('%Y/%m/%d')}")
        lcd.cursor_pos = (1, 0)
        lcd.write_string(f"Time: {time.strftime('%H:%M:%S')}")
    except RuntimeError as error:
        print(error.args[0])

    time.sleep(0.5)
```

## 4. 結果

![]({{ "images/2019-11-15/IMG_0521.JPG" | relative_url }})   

## 5. 與昨天的溫度感測器做整合
```python
import board
import time
import adafruit_dht
from RPLCD.i2c import CharLCD

pin = board.D17 # GPIO 17
dhtDevice = adafruit_dht.DHT22(pin)

lcd = CharLCD('PCF8574', address=0x27, port=1, backlight_enabled=True)
lcd.clear()


while True:
    try:
        lcd.cursor_pos = (0, 0)
        lcd.write_string(f"Date: {time.strftime('%Y/%m/%d')}")
        lcd.cursor_pos = (1, 0)
        lcd.write_string(f"Time: {time.strftime('%H:%M:%S')}")
 
        time.sleep(3)

        temperature = dhtDevice.temperature
        humidity = dhtDevice.humidity
       
        lcd.clear()
        lcd.cursor_pos = (0, 0)
        lcd.write_string(f"Temp: {temperature} *C")
        lcd.cursor_pos = (1, 0)
        lcd.write_string(f"Hum : {humidity} %")
        
        time.sleep(3)
    except RuntimeError as error:
        print(error.args[0])
```