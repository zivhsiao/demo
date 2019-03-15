---
title: LINE & FB Messenger 結合 wit.ai 來處理使用者發問的問題
categories:
- ChatBot
excerpt: |
  我寫了 LINE Bot，而這次專門寫與 FB Messenger 結合，然後專門用 wit.ai 寫一些問題改如何去做
feature_text: |
  # LINE & FB Messenger 結合 wit.ai
  LINE & FB Messenger 結合 wit.ai
feature_image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/milwaukee_1920x1278.jpg" 
image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/line_api/74187-mobile-messaging-chatting_1920x1285.jpg"  
---

LINE & FB Messenger 結合 wit.ai 來處理使用者發問的問題，瞭解一下，實作上並不會太難

<!-- more -->

### 先取得 LINE or FB Messenger 的使用者的發問的問題

#### LINE 取得方式

```php
$jsonString = file_get_contents('php://input');
$jsonObj = json_decode($jsonString);

// 使用者發出一則文字
$event = $jsonObj->{"events"}[0];
$type = $event->{"message"}->{"type"};
// 發問的文字
$messageText = $event->{"message"}->{"text"};
// 回應的 token
$replyToken = $event->{"replyToken"};
// 使用者的 ID
$userId = $event->{"source"}->{"userId"};
```

#### FB Messanger 取得方式

```php
$input = json_decode(file_get_contents('php://input'), true);
$inputMessage = $input['entry'][0]['messaging'][0];
// 使用者的 ID
$senderId = $inputMessage['sender']['id'];
// 發問的文字
$messageText = $inputMessage['message']['text'];
```


### 再取得 wit.ai 的回應，然後才能給它不同的指令 

```php
// wit.ai 需要的 token
$witToken = 'YOUR TOKEN';
// 上面的發問的文字
$wit_url = "https://api.wit.ai/message?v=20171020&q=".$messageText;
$wit_auth = array("Authorization: Bearer ". $witToken);
$ch = curl_init();
curl_setopt($ch,CURLOPT_URL, $wit_url);
curl_setopt($ch, CURLOPT_HTTPHEADER, $wit_auth);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$response = curl_exec($ch);
curl_close($ch);

$response = json_decode($response,true);
```

#### 取到回應的文字
```php
$resEntities = $response['entities'];
if(!empty($resEntities['greetings'][0]['value'])){
    if($resEntities['greetings'][0]['value'] == true){
        $answer = '你好，有什麼可以協助你？';    
    }
}

if(!empty($resEntities['location'][0]['value'])){
    $answer =  $resEntities['location'][0]['value'];
}

if(empty($answer)){
    $answer = '您好，我不太瞭解您所提出的問題！';
}
```

到這裏大致先完成基本的處理