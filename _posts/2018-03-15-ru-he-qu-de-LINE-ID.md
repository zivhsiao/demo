---
title: 如何可以取得 LINE 的 ID
categories:
- LINE
excerpt: |
  這是我寫了 LINE 機器人的一些心得，先從 LINE Developer 開始吧
feature_text: |
  # 如何可以取得 LINE 的 ID
  LINE 的 ID
feature_image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/milwaukee_1920x1278.jpg" 
image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/line_api/截圖 2019-03-11 下午1.45.56.png"  
---

LINE Bot 的剛開始要做什麼，因爲有的沒有接觸過 LINE 機器人，所以寫了這篇文章

<!-- more -->

### 首先，進入 LINE@ 的開發者界面
![截圖 2019-03-11 下午1.45.56](https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/line_api/截圖 2019-03-11 下午1.45.56.png)
 
### 進入它的 Messaging settings
1. 進入 Provider List 的項目，若沒有新增即可
2. 產生新的 Channel 或者已經有的就可以
3. 立即可以看到 access token，如圖所示

![LINE_Developers](https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/line_api/LINE_Developers.png)

### 除了 access toen 之外，還需要 Webhook 啓用
1. Use webkooks -> Enabled
2. Webkhook URL，它必須要是 https 的網頁，然後可以驗證過就沒有問題
3. Allow bot to join group chats：這個羣組功能是否啓用，因爲直接是 1 對 1，所以這部分 Disabled

![LINE_Developers_2](https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/line_api/LINE_Developers_2.png)


### 開發 LINE@ 初步
只要使用者加入 LINE@，立即就可以得到 replyToken, userId 兩個參數
以下是 Codeigniter 寫的
```php
//從Line Developer上申請的Token
$accessToken = '申請的 access token'

//取得機器人丟過來的訊息
$jsonString = file_get_contents('php://input');

//轉成JSON
$jsonObj = json_decode($jsonString);

//設定變數給JSON的各欄位
$event = $jsonObj->{"events"}[0];
$type = $event->{"message"}->{"type"};
$message = $event->{"message"};
$replyToken = $event->{"replyToken"};
$userId = $event->{"source"}->{"userId"};

// 第一次使用記錄下來
$this->db->where('line_id', $userId);
$chk = $this->db->get('line_account')->result_array();

if(count($chk) == 0){
    $dataInsert = array(
        "line_id" => $userId
    );

    $this->db->insert('line_account', $dataInsert);
}
```

- replyToken：回應的 token，必須給每個傳送的 message 才有用處
- userId：每個 user ID，這會隨着使用者加入的 LINE@ 而有所不同。例如同樣的使用者，加入 App1，App2 所產生的 ID 各不相同