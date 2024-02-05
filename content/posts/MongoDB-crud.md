+++
author = "SW"
title = '[資料庫]MongoDB CRUD：新增Create、查詢Read、修改Update和刪除Delete'
date = 2024-02-05T11:28:56+08:00
description = '使用新增Create、查詢Read、修改Update和刪除Delete等方式，管理MongoDB中的資料文件。'
tags = [
    "database",
    "MongoDB",
    "CRUD"]
series = ["MongoDB"]
categories = ["資料庫"]
+++

## **Create** 新增：用 Python 在 MongoDB 新增 JSON 格式資料

### 新增一筆資料 (insert_one()方法)

```python
集合.insert_one(文件資料)

collection = db.website
collection.insert_one({
					   "name":kyu,
					   "password":123

})

```

- 每一筆存入文件的資料，都有獨立編號(MongoDB 自動產生 ID)

```python
collection = db.website
result = collection.insert_one({
					   "name":kyu,
					   "password":123

})
print(result.inserted_id)
```

### 新增多筆資料 (insert_many 方法)

```python
集合.insert_many([文件資料,文件資料,文件資料])
```

- 按順序取得每一筆資料的編號

```python
collection = db.website
result = collection.insert_many([{
		"name":kyu,
		"password":123

	},{ "name":tek,
		"password":456

}])
print(result.inserted_ids)
```

## **Read** 讀取：取得集合中的資料

### 取得集合中**第一筆**文件資料(find_one())

```python
集合.find_one()
```

```python
collection = db.users #要操作的集合

data = collection.find_one()
print(data)

```

### 根據**編號**取得文件資料

- 文件編號是 ObjectId 物件，須從 bson.objectid 封包載入

```python
集合.find_one(文件編號)
```

```python
from bson.objectid import ObjectId


collection = db.users #要操作的集合

data = collection.find_one(
		ObjectId("12345")
)
print(data)
```

### 取得文件中欄位資料

```python
文件資料["欄位名稱"]
```

```python
collection = db.users #要操作的集合

data = collection.find_one()
print(data["_id"])
print(data["name"])
```

### 取得所有文件資料

```python
集合.find()
```

```python
collection = db.users
cursor = collection.find()
print(cursor) #Cursor物件

#使用for迴圈逐一取得文件
for doc in cursor:
print(doc)
print(doc["name"]) #取得特定資料
```

## **Update** 更新：文件中的欄位資料

### 更新**符合條件**一筆文件欄位

```python
集合.update_one(篩選條件,更新的資訊)
```

```python
collection = db.users
collection.update_one({
	"email":"test@test.com"
},{"$set":{ %% set資料覆蓋過去的意思  %%
		   "password":"test123"
}
  })
```

### 更新多筆文件欄位

```python
集合.update_many(篩選條件,更新的資訊)
```

```python
collection = db.users
collection.update_many({
	"level":2
},{"$set":{ %% set資料覆蓋過去的意思  %%
		   "role":"editor"
}
  })
```

- 覆蓋/新增欄位 `$set`
- 加減數字欄位 `$inc`

  ```python
  collection = db.users
  collection.update_many({
  	"email":"test@test.com"
  },{"$inc":{
  		   "level":2 %% 減的話負數 %%
  }
  })
  ```

- 乘除數字欄位 `$mul`

```python
 collection = db.users
collection.update_many({
	"email":"test@test.com"
},{"$mul":{
		   "level":0.5    #除
}
 })
```

- 清除欄位 `$unset` : 整個欄位刪掉

```python
  collection = db.users
collection.update_many({
	"email":"test@test.com"
},{"$unset":{
		   "name":1
}
  })
```

### 查看更新一筆文件的結果

```python
collection = db.users
result = collection.update_one({
	"email":"test@test.com"
},{"$set":{ %% set資料覆蓋過去的意思  %%
		   "password":"test123"
}
  })

# 符合篩選條件的文件數量
print(result.matched_count)

# 實際完成更新的文件數量
print(result.modified_count)

```

### 查看更新多筆文件的結果

```python
collection = db.users
result = collection.update_many({
	"level":2
},{"$set":{ %% set資料覆蓋過去的意思  %%
		   "role":"editor"
}
  })

# 符合篩選條件的文件數量
print(result.matched_count)

# 實際完成更新的文件數量
print(result.modified_count)

```

## **Delete** 刪除資料

### 刪除一筆文件資料

```python
集合.delete_one(篩選條件)
```

```python
collection = db.users
result = collection.delete_one(
					  {"e-mail":"test1@test.com"}
)

print(result.deleted_count)
```

### 刪除多筆文件資料

```python
集合.delete_many(篩選條件)
```

```python
collection = db.users
result = collection.delete_many(
					  {"level":1}
)
print(result.deleted_count)

```
