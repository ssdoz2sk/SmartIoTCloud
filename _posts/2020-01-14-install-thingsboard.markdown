---
layout: post
title:  "安裝 ThingsBoard"
date:   2020-01-14 18:09:11 +0800
categories: ThingsBoard
---

# ThingsBoard 安裝

## 1. 基本需求
1. Java JDK >= 8
> 想要確認 Java 版本可以下 (`java -version`)

## 2. 安裝 ThingsBoard 與資料庫建立
1. 下載安裝包   
    ```bash
    wget https://github.com/thingsboard/thingsboard/releases/download/v2.4.3/thingsboard-2.4.3.deb
    ```   
    ![]({{ "images/2020-01-14/螢幕快照_2020-01-14_18-52-17.png" | relative_url }})   

2. 安裝 ThingsBoard
    ```bash
    sudo dpkg -i thingsboard-2.4.3.deb
    ```
    ![]({{ "images/2020-01-14/螢幕快照_2020-01-14_19-19-59.png" | relative_url }})   

3. 設定 ThingsBoard 的資料庫 PostgreSQL
    ```bash
    sudo apt install postgresql postgresql-contrib
    ```

4. 啟動資料庫服務
    ```bash
    sudo systemctl start postgresql
    ```

5. 進入到 `postgres` 的使用者帳戶建立資料庫
    ```bash
    sudo su - postgres
    ```
    如果尚未建立預設密碼，請先下去修改預設密碼   
    ```bash
        psql
        \password 
        \q
    ```

    進入到 postgres 的 cli
    ```bash
        psql -U postgres -d postgres -h 127.0.0.1 -W
    ```

    然後建立資料庫並離開到一般使用者的終端
    ```bash
    CREATE DATABASE thingsboard;
    ```
    ![]({{ "images/2020-01-14/螢幕快照_2020-01-14_19-20-16.png" | relative_url }})   

6. 修改ThingsBoard 的資料庫設定
    ```bash
    sudo vim /etc/thingsboard/conf/thingsboard.conf
    ```

    請把以下設定貼到該 conf 檔案的最後面，並把最後一行的 "修改成你的資料庫密碼"改成你正確的資料庫密碼
    ```conf
    export DATABASE_ENTITIES_TYPE=sql
    export DATABASE_TS_TYPE=sql
    export SPRING_JPA_DATABASE_PLATFORM=org.hibernate.dialect.PostgreSQLDialect
    export SPRING_DRIVER_CLASS_NAME=org.postgresql.Driver
    export SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/thingsboard
    export SPRING_DATASOURCE_USERNAME=postgres
    export SPRING_DATASOURCE_PASSWORD=修改成你的資料庫密碼
    ```

7. 請下以下指令進行安裝
    ```bash
    sudo /usr/share/thingsboard/bin/install/install.sh
    ```
    > 如果安裝想要建立一個測試測試介面，就可以再多個 `--demo` 參數

8. 如果安裝成功的，就把服務建起來吧
    ```
    sudo systemctl start thingsboard
    ```

## 3. 再來打開 <a href="http://127.0.0.1:8080">http://127.0.0.1:8080</a>
以下是預設帳號密碼   
> Systen Administrator:    
>>    sysadmin@thingsboard.org / sysadmin   

如果安裝時有開 --demo 參數的話，還有這兩組帳密   
>Tenant Administrator:    
>>    tenant@thingsboard.org / tenant    
>Customer User:    
>>    customer@thingsboard.org / customer    
   
> 我建在虛擬機內，IP就不一樣啦   

![]({{ "images/2020-01-14/螢幕快照_2020-01-14_20-24-27.png" | relative_url }})   

再來就是進入右上角的個人屬性，把帳號密碼給改一改吧
![]({{ "images/2020-01-14/螢幕快照_2020-01-14_20-14-32.png" | relative_url }})   


設定其他使用者、建立裝置設備、資料上傳，我們就下個文章再說吧。


