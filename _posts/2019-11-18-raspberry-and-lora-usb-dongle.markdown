---
layout: post
title:  "Raspberry & LoRa USB Dongle"
date:   2019-11-18 20:09:11 +0800
categories: RaspberryPi LoRa
---

# Raspberry & LoRa 模組

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})

## 2. LoRa USB Dongle 插上樹莓派的 USB 孔

## 3. 程式碼
> LoRa.py   
   
```python
import serial
import platform

from serial import SerialException
import time
import sys
import glob
from functools import reduce
from builtins import Exception

class CRCCheckError(Exception):
    pass

class LoRa:
    def __init__(self):
        try:
            # 獲取 serial 位址
            self.portPath = self.getPortPath()
            print("Rola port path: {}".format(self.portPath))
            self.ser = serial.Serial(self.portPath, 115200, timeout=3)
        except SerialException:
            print("port already open")

    def serial_allPorts(self):
        """ Lists serial port names
            :raises EnvironmentError:
                On unsupported or unknown platforms
            :returns:
                A list of the serial ports available on the system
        """
        if sys.platform.startswith('win'):
            ports = ['COM%s' % (i + 1) for i in range(256)]
        elif sys.platform.startswith('linux') or sys.platform.startswith('cygwin'):
            # this excludes your current terminal "/dev/tty"
            ports = glob.glob('/dev/tty[A-Za-z]*')
        elif sys.platform.startswith('darwin'):
            ports = glob.glob('/dev/tty.*')
        else:
            raise EnvironmentError('Unsupported platform')
        result = []
        for port in ports:
            try:
                s = serial.Serial(port)
                s.close()
                result.append(port)
            except (OSError, serial.SerialException):
                pass
        return result

    # 計算CRC 檢查碼

    def check_CRC(self, data):
        return reduce(lambda r, d: r^d, data, 0)

    def serial_ports(self):
        """ Lists serial port names

          :raises EnvironmentError:
              On unsupported or unknown platforms
          :returns:
              A list of the serial ports available on the system
        """
        if sys.platform.startswith('win'):
            ports = ['COM%s' % (i + 1) for i in range(256)]
        elif sys.platform.startswith('linux') or sys.platform.startswith('cygwin'):
            # this excludes your current terminal "/dev/tty"
            ports = glob.glob('/dev/tty[A-Za-z]*')
        elif sys.platform.startswith('darwin'):
            ports = glob.glob('/dev/tty.*')
        else:
            raise EnvironmentError('Unsupported platform')
        result = []
        for port in ports:
            try:
                if port != "/dev/tty.Bluetooth-Incoming-Port" and port != "/dev/ttyAMA0":
                    s = serial.Serial(port)
                    s.close()
                    result.append(port)
            except (OSError, serial.SerialException):
                pass
        return result

    #  get all USB Uart Port.
    def getPortPath(self):
        OSVersion = platform.system()
        port_path = "/dev/cu.usbserial"

        if OSVersion == "Darwin":  # MAC Port
            port_path = "/dev/cu.usbserial-A700eGFx"
        elif OSVersion == "Linux":  # Linux Port
            port_path = "/dev/ttyUSB0"
        ports = self.serial_ports()
        
        for port_path in ports:
            # 判對是否是LoRa 接在上面
            try:
                self.ser = self.ser = serial.Serial(port_path, 115200, timeout=3)
                
            except SerialException:
                print("port already open")
            data = self.getChipID()
            if(len(data) > 1):
                return port_path
        return None

    def arrayToHexString(self, array1):
        print("AR: {}".format(array1))
        s = ''.join('{:02x}'.format(x) for x in array1)
        return s
        # return array1.hex()

    def arrayToHexStringKeepPureData(self, array1):
        length = array1[2]
        crc = array1[len(array1)-1]
        check_crc = self.check_CRC(array1[:len(array1)-1])
        array1 = array1[3:length+1]
        if not crc == check_crc:
            raise CRCCheckError(f'{crc} and {check_crc} is not the same')

        str2 = self.arrayToHexString(array1)
        return str2

    # 送byte 到　Chip 上
    def FunLora_ChipSendByte(self, array1):
        try:
            self.ser.write(serial.to_bytes(array1))
            time.sleep(0.02)
            bytesToRead = self.ser.inWaiting()
            data = self.ser.read(bytesToRead)
        except (OSError, serial.SerialException):
            pass
        return data

    def Fun_ser_Write(self, array1):
        try:
            self.ser.write(serial.to_bytes(array1))
        except SerialException:
            print("Fun_ser_Write error")

    # close Serial Port

    def FunLora_close(self):
        try:
            self.ser.close()
        except SerialException:
            print("port already open")

    # 讀取晶片ID
    def getChipID(self):
        array1 = [0x80, 0x00, 0x00, 0]
        array1[3] = self.check_CRC(array1)
        
        self.Fun_ser_Write(array1)
        time.sleep(0.01)
        bytesToRead = self.ser.inWaiting()
        data = self.ser.read(bytesToRead)
        print("ChipID: {}".format(data.hex()))
        return data

    # 重置 & 初始化
    def reset(self):
        array1 = [0xc1, 0x01, 0x00, 0]
        array1[3] = self.check_CRC(array1)
        data = self.FunLora_ChipSendByte(array1)
        print("Init Response: {}".format(data.hex()))
        return data

    # 讀取設定狀態
    def readSetup(self):
        array1 = [0xc1, 0x02, 0x00, 0]
        array1[3] = self.check_CRC(array1)
        data = self.FunLora_ChipSendByte(array1)
        print("Read Setup Response: {}".format(data.hex()))
        return data

    # 設定讀取和頻段
    def setRX(self):
        array1 = [0xC1, 3, 5, 3, 1, 0x65, 0x6C, 0xf, 0]
        array1[8] = self.check_CRC(array1)
        data = self.FunLora_ChipSendByte(array1)
        print("Set RX Response: {}".format(data.hex()))
        return data

    # 讀取LoRa 傳過來的資料
    def read(self):
        array1 = [0xC1, 0x6, 0x0, 0]
        array1[3] = self.check_CRC(array1)
        data = self.FunLora_ChipSendByte(array1)
        print("Read: {}".format(data.hex()))
        return data

    # 設定寫入和頻段
    def setTX(self):
        array1 = [0xC1, 3, 5, 2, 1, 0x65, 0x6C, 0xf, 0]
        array1[8] = self.check_CRC(array1)
        data = self.FunLora_ChipSendByte(array1)
        print("Set TX Response: {}".format(data.hex()))
        return data

    # 寫入測試
    def write_test(self):
        array1 = [0xC1, 0x5, 0x5, 0x61, 0x62, 0x63, 0x64, 0x65, 0]
        array1[8] = self.check_CRC(array1)
        data = self.FunLora_ChipSendByte(array1)
        print("Write Response: {}".format(data.hex()))
        return data

    # 寫入資料 c1 05 長度 data CRC
    # Example： 資料是 01 02 03
    # 寫入：   c1 05 03 01 02 03 CRC 
    def write16bytesArray(self, data_array):
        TX_Data = data_array
        CMD_Data = [0xc1, 0x05]
        CMD_Data.append(len(TX_Data))
        for i3 in data_array:
            CMD_Data.append(ord(i3))
        
        CRC = self.check_CRC(CMD_Data)
        CMD_Data.append(CRC)
        print(CMD_Data)
        data = self.FunLora_ChipSendByte(CMD_Data)
        return data

    # 讀取LoRa 傳過來的資料
    def readCounter(self):
        array1 = [0xC1, 0x7, 0x0, 0]
        array1[3] = self.check_CRC(array1)
        data = self.FunLora_ChipSendByte(array1)
        print("Read: {}".format(data.hex()))
        return data
```
> 讀取 main.py   

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

> 發送 main.py

```python
import Lora
import time
import os
import serial


def Fun_main():
    LoRa = Lora.LoRa()
    LoRa.reset()
    LoRa.readSetup()

    # 設定寫入和頻段
    LoRa.setTX()
    try:
        while True:
            ts = time.strftime(f'%Y/%m/%d %H:%M:%S')
            print(ts)
            LoRa.write16bytesArray(ts)
            time.sleep(1)

    except:
        print("quit")


if __name__ == '__main__':
    Fun_main()
```
## 4. 結果

![]({{ "images/2019-11-18/螢幕快照_2019-11-25_22-57-57.png" | relative_url }})   
