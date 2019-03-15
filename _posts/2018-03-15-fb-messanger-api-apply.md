---
title: FB Messager API 的申請
categories:
- Facebook
excerpt: |
  說明 FB Messager API，主要是你要有粉絲團，然後 Facebook Developer 建立一個應用程式就可以
feature_text: |
  # FB Messanger API 的申請
  FB Messager API 的申請經過
feature_image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/milwaukee_1920x1278.jpg" 
image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/milwaukee_1920x1278.jpg"  
---

今天說明 FB Messager API，主要是你要有粉絲團，然後 Facebook Developer 建立一個應用程式就可以了

其實 [Facebook 官方教學](https://developers.facebook.com/docs/messenger-platform/quickstart) 已經寫的很詳細了，在這裏只是 PO 出來自己的心得


<!-- more -->

### 建立一個 Facebook 的應用程式
必須先建立一個應用程式，例如：測試網站
![FB](https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/facebook_api/Facebook_for_Developers_1.png)

按下安全驗證
![FB](https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/facebook_api/Facebook_for_Developers.png)

### 必須要有粉絲頁
請你一定要 facebook 底下有粉絲頁才可以

### 詳細 Facebook Developer 應用程式的說明

選擇左邊的選單，在產品的旁邊按下 + 
![FB](https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/facebook_api/測試網站_-_情境_-_Facebook_for_Developers.png)

然後有幾個 API，這時必須選擇 Messenger，然後按下設定
![FB](https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/facebook_api/測試網站_-_主控板_-_Facebook_for_Developers.png)

會進入權杖產生以及 webhook 設定頁面，選擇粉絲頁，就有粉絲頁存取權杖
![FB](https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/facebook_api/測試網站_-_Messenger_-_Facebook_for_Developers.png)

按下設定webhooks，可以看到回呼網址，驗證權杖，訂閱欄位，這裏有個 messages 請打勾
- 回呼網址：這裏一定要有 https 的網址，指向過去就可以了
- 驗證權杖：一定要跟後面的 $hubVerifyToken 要一致
![FB](https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/facebook_api/測試網站_-_Messenger_-_Facebook_for_Developers_1.png)


#### 到這裏需要寫程式了，這裏有幾點說明
- AccessToken：可以隨便你去寫，就是與前面的驗證權杖是一模一樣就可以了
- FB_TOKEN：就是你的粉絲頁存取權杖

```php
$hubVerifyToken = 'AccessToken'; // 這裏寫的字串，後面我們設定 webhooks 時要一致
$accessToken = FB_TOKEN;

// check token at setup
if (!empty($_REQUEST['hub_mode']) && $_REQUEST['hub_mode'] == 'subscribe') {
    if($_REQUEST['hub_verify_token'] == $hubVerifyToken) {
        echo $_REQUEST['hub_challenge'];
        exit;
    }
}
```

#### 開始接收使用者輸入的文字
這裏接收的是 sender.id，message.text
```php
$input = json_decode(file_get_contents('php://input'), true);
$inputMessage = $input['entry'][0]['messaging'][0];
$senderId = $inputMessage['sender']['id'];
$messageText = $inputMessage['message']['text']
```

這裏是使用者說說明，馬上就回什麼
```php
$answer = $messageText;
```

最後再回傳給 fb 的 server 就可以了
```php
$response = [
    'recipient' => [ 'id' => $senderId ],
    'message'   => [ 'text' => $answer ]
];

$ch = curl_init('https://graph.facebook.com/v3.2/me/messages?access_token='.$accessToken);
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($response));
curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
curl_exec($ch);
curl_close($ch);
```

這樣就結束了，是否寫的還算可以呢？