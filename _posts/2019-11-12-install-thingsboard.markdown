---
layout: post
title:  "安裝 ThingsBoard"
date:   2019-11-12 10:15:56 +0800
categories: ThingsBoard install
---

# Ubuntu 的 ThingsBoard 安裝
ThingsBoard 是一個 Open Sourse 的 IoT 平台，可以自己去架設服務，通過該服務自己上傳資料，管理資料，或是外接自己的分析系統等等。   

## 1. 安裝 Java 8
ThingsBoard 的服務依賴於 Java 8，不過經測試 ~ Java 12 服務都可以正常使用   
```bash
sudo apt update
sudo apt install openjdk-8-jdk
```

設置作業系統預設的 Java 版本   
```bash
sudo update-alternatives --config java
```
如果 出現 There is only one alternative in link group java.    
就代表系統內只有一個 java 環境，不需要做更動。   

## 2. 安裝 ThinksBoard

### 1. 獲取程式
```bash
wget https://github.com/thingsboard/thingsboard/releases/download/v2.4.1/thingsboard-2.4.1.deb
```

### 2. 安裝
```bash
sudo dpkg -i thingsboard-2.4.1.deb
```

## 3. 安裝資料庫
一般我們的資料不會超過 5k msg/s ，但為了教學，我們還是把兩個資料庫(PostgreSQL + Cassandra) 都裝起來   

### 1. PostgreSQL
```bash
sudo apt-get install postgresql postgresql-contrib
sudo service postgresql start
```

#### 建立新使用者
進入到 PostgreSQL 帳號   
```bash
sudo su - postgres
```

進入到 PostgreSQL Cli   
```bash
psql
```

設定密碼並離開   
```psql 
\password
\q
```

#### 建立資料表
```psql
psql -U postgres -d postgres -h 127.0.0.1 -W
CREATE DATABASE thingsboard;
\q
```

### 2.Cassandra
Cassandra 的相關套件並沒有在 Ubuntu 內建的 PPA 中，所以我們要自己增加第三方的   

#### 新增套件源
``` bash
echo 'deb http://www.apache.org/dist/cassandra/debian 311x main' | sudo tee --append /etc/apt/sources.list.d/cassandra.list > /dev/null
curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
```

#### 安裝
```bash
apt install cassandra cassandra-tools
```

### 3. 編輯 ThingsBoard 的資料庫設定
```bash
sudo vim /etc/thingsboard/conf/thingsboard.conf
```

在最後面增加以下這幾行，第八行要改成你用的密碼喔   
```config
# DB Configuration 
export DATABASE_ENTITIES_TYPE=sql
export DATABASE_TS_TYPE=cassandra
export SPRING_JPA_DATABASE_PLATFORM=org.hibernate.dialect.PostgreSQLDialect
export SPRING_DRIVER_CLASS_NAME=org.postgresql.Driver
export SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/thingsboard
export SPRING_DATASOURCE_USERNAME=postgres
export SPRING_DATASOURCE_PASSWORD=PUT_YOUR_POSTGRESQL_PASSWORD_HERE

export CASSANDRA_CLUSTER_NAME=Thingsboard Cluster
export CASSANDRA_KEYSPACE_NAME=thingsboard
export CASSANDRA_URL=127.0.0.1:9042
export CASSANDRA_USE_CREDENTIALS=false
export CASSANDRA_USERNAME=
export CASSANDRA_PASSWORD=
```

## 4. 執行 ThingsBoard 安裝的 script
```bash
sudo /usr/share/thingsboard/bin/install/install.sh
```

## 5. 啟動 ThingsBoard 的服務
```bash
sudo systemctl start thingsboard
```

![]({{ "images/2019-11-12/螢幕快照_2019-11-12_18-29-04.png" | relative_url }})   
如果沒有問題，可以執行以下讓他預設開機執行   
```bash
sudo systemctl enable thingsboard
```

## 6. 檢視網站
然後你就可以到 [http://localhost:8080/](http://localhost:8080/) 看看了   

預設帳號密碼： `sysadmin@thingsboard.org` / `sysadmin`   

然後你可能想問：我要開放其他人註冊呢...   
抱歉~ PE 版才有喔，不然你要自己寫了 OAO//   
[Self-registration (PE)](https://thingsboard.io/docs/user-guide/self-registration/)