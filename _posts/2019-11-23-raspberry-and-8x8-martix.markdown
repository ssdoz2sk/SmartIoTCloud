---
layout: post
title:  "Raspberry & 8x8 矩陣"
date:   2019-11-23 18:09:11 +0800
categories: RaspberryPi Sensor
---

# Raspberry & 8x8 LED 矩陣

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})
2. 安裝 matrix 7219 套件
```bash
git clone https://github.com/rm-hull/max7219.git
cd max7219
sudo python setup.py install
```
   
> 如果是 pillow 如果安裝失敗，出現 `The headers or library files could not be found for jpeg`，可以下:   
> `sudo apt-get install libjpeg-dev zlib1g-dev`   

## 2. 將 8x8 矩陣接上插上樹莓派

| 接腳 | GPIO |
| --- | ---- |
| Vcc | 5V |
| GND | GND |
| DIN | MOSI (GPIO10, pin19) |
| CS | SPI CE0 (GPIO8, pin24) |
| CLK | SPI CLK (GPIO11, pin23) |

![]({{ "images/2019-11-23/2019-11-23_Matrix_Led_Modul_bb.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-23_Matrix_Led_Modul.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
在矩陣內輸出 a-z 字母
```python
import string
from luma.led_matrix.device import max7219
from luma.core.legacy import show_message
from luma.core.legacy.font import proportional, LCD_FONT
from luma.core.interface.serial import spi, noop


serial = spi(port=0, device=0, gpio=noop())
device = max7219(serial, cascaded=1, block_orientation=0,
                 rotate=2, blocks_arranged_in_reverse_order=False)
msg = string.ascii_lowercase

show_message(device, msg, fill="white", font=proportional(LCD_FONT), scroll_delay=0.1)
```

## 4. 結果

![]({{ "images/2019-11-23/IMG_0569.JPG" | relative_url }})   
