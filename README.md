---
description: 資料庫設計工具
---

# dbdiagram-資料庫設計工具

## [dbdiagram](https://dbdiagram.io/home) 筆記

### 介紹

在資料庫(database 後簡稱DB)表格(table)設計中，個人是使用draw.io進行關聯圖製作，但是關聯的線以及圖表繪製都必須手動操作，步驟繁瑣且不美觀。

但dbdiagram.io則是利用簡單的語法將table的ER Model完整呈現，讓table設計看起來更為簡單、明瞭！

而在進行DB的逆向工程時，多數table設計並沒有foreign key，導致DB的IDE工具所繪製出來的ER Model是沒有關聯性的！

此時dbdiagram.io就是一個好幫手，工具自帶匯入SQL的功能`(但僅支持MySQL、PostgreSQL、Rails、SQL Server)`，可以快速產生table圖表！當圖表產生後，就可以加入語法，讓table之間的關聯性展現出來，至此就可以做出完整的ER Model了！

### 說明

#### 畫面操作說明

![dbdiagram.io開發畫面](https://i.imgur.com/vhfv8Ti.png)&#x20;

畫面左邊就是語法撰寫的地方，右邊則是table呈現的方式(此工具是所見即所得)

以下功能都必須登入帳號後才能使用(目前支援github、google帳號)

> * Save 儲存
> * Share 分享
> * Export 匯出 (可選擇匯出PDF、SQL、PNG)
> * Import 匯入 (目前僅支援MySQL、PostgreSQL、Rails、SQL Server)

另外在畫面上可以看到Analytics Guidebook的按鈕！ **這是一本免費的電子書，內容是講解數據分析與設計**，有興趣的人可以自行下載來觀看(全英文)，如果不想留個資也可以選擇[線上觀看](https://www.holistics.io/books/setup-analytics/start-here-introduction/)。

#### 語法(DBML)說明

> * // 註解
> * Table 建立表格
> * as 別名
> * Ref: a.column > b.column foreign key設定
> * a.column \[ref: > b.column] foreign key設定 `> 是指1->* < 是指 *->1 -是指 1->1`
> * Indexes
> * \[] 備註

```
//// -- LEVEL 1
//// -- Tables and References

// Creating tables
Table users as U {
  id int [pk, increment] // auto-increment
  full_name varchar
  created_at timestamp
  country_code int
}

Table countries {
  code int [pk]
  name varchar
  continent_name varchar
 }

// Creating references
// You can also define relaionship separately
// > many-to-one; < one-to-many; - one-to-one
Ref: U.country_code > countries.code  
Ref: merchants.country_code > countries.code

//----------------------------------------------//

//// -- LEVEL 2
//// -- Adding column settings

Table order_items {
  order_id int [ref: > orders.id] // inline relationship (many-to-one)
  product_id int
  quantity int [default: 1] // default value
}

Ref: order_items.product_id > products.id

Table orders {
  id int [pk] // primary key
  user_id int [not null, unique]
  status varchar
  created_at varchar [note: 'When order created'] // add column note
}

//----------------------------------------------//

//// -- Level 3 
//// -- Enum, Indexes

// Enum for 'products' table below
Enum products_status {
  out_of_stock
  in_stock
  running_low [note: 'less than 20'] // add column note
}

// Indexes: You can define a single or multi-column index 
Table products {
  id int [pk]
  name varchar
  merchant_id int [not null]
  price int
  status products_status
  created_at datetime [default: `now()`]
  
  Indexes {
    (merchant_id, status) [name:'product_status']
    id [unique]
  }
}

Table merchants {
  id int
  country_code int
  merchant_name varchar
  
  "created at" varchar
  admin_id int [ref: > U.id]
  Indexes {
    (id, country_code) [pk]
  }
}

Table merchant_periods {
  id int [pk]
  merchant_id int
  country_code int
  start_date datetime
  end_date datetime
}

Ref: products.merchant_id > merchants.id // many-to-one
//composite foreign key
Ref: merchant_periods.(merchant_id, country_code) > merchants.(id, country_code)

```
