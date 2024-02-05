+++
author = "SW"
title = '[資料庫] MongoDB 環境建置 & 資料結構'
date = 2024-02-05T10:59:43+08:00
description = 'MongoDB是一個開源的NoSQL資料庫管理系統，以文件導向的方式儲存資料，適用於處理非結構化或半結構化數據。'
tags = [
    "database",
    "MongoDB"]
series = ["MongoDB"]
categories = ["資料庫"]
+++

## 建置方式(流程)

### 官方雲端

1. 註冊帳號
2. 建立組織
3. 建立專案
4. 建立存取權限(Network Access)
5. 建立叢集 Cluster
6. 程式測試(Python 連線資料庫)

   - 安裝套件：pymongo

   ```python
   $ python -m pip install pymongo
   ```

   > PyMongo requires dnspython to support mongodb+srv:// connection strings and MongoDB Atlas. dnspython is automatically installed when installing or upgrading to the latest PyMongo version.

   - 撰寫連線資料庫程式，進行第一次資料寫入，確認測試成功。

### 本機安裝＆管理

## 資料結構

### 資料儲存的方式：三層式結構

- 資料庫 (A 網站)
  - 集合 (會員資料)
    - 文件 (會員一)

### 連線，將資料傳入 MongoDB

```python
import pymongo



# 載入
from pymongo.mongo_client import MongoClient

uri = "mongodb+srv://<帳密>@mycluster.mack.mongodb.net/?retryWrites=true&w=majority"



# Create a new client and connect to the server
client = MongoClient(uri)

# Send a ping to confirm a successful connection
try:
client.admin.command("ping")
print("Pinged your deployment. You successfully connected to MongoDB!")
except Exception as e:
print(e)
# 用於測試電腦網路中主機之間的連通性。具體來說，ping 會發送一種特殊類型的網路控制消息，這種消息被稱為 ICMP 回顯請求（ICMP Echo Request），然後等待接收主機回應。如果接收到回應，就表示兩台主機之間存在網路連接。

# 這個術語源自 sonar 技術中用於確認目標存在的聲納信號，而在計算機網路中，"ping" 就成為了確認網路連接的一種常用方式。


#把資料放進資料庫
db = client.test # 選擇要操作的資料庫test

collection = db.users # 選擇要操作的集合users



# 把文件document存入collection中
collection.insert_one(

{
"name": "test",
"gender": "male",
} #每個會員一個dict

)
print("Update Successfully!")

```
