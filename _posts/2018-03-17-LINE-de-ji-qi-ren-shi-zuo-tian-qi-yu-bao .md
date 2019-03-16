---
title: LINE@ 的機器人，實作天氣預報
categories:
- LINE
excerpt: |
  這個剛好寫了一個 LINE 的機器人，用途是天氣預報，可以問它「天氣」、「新竹市的天氣」一系列的問題
feature_text: |
  # LINE@ 的機器人，實作天氣預報
  實作 LINE 的機器人，用途是天氣預報
feature_image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/milwaukee_1920x1278.jpg" 
image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/line_bot/0509-LINE-BOT.jpg"  
---

實作了 LINE 的機器人，用途是天氣預報，你可以問它「天氣」、「新竹市的天氣」一系列的問題


<!-- more -->

#### LINE 的申請
這就不再多說了，自己看吧！[LINE 的申請](https://www.prgpress.com/line/2018/03/15/ru-he-qu-de-LINE-ID)

#### 這是接收使用者輸入的文字

```php
$input = json_decode(file_get_contents('php://input'), true);
$inputMessage = $input['entry'][0]['messaging'][0];
$senderId = $inputMessage['sender']['id'];
$messageText = $inputMessage['message']['text'];
```

#### 這是加入 wit.ai 寫成 class

這個就是丟給它一串文字，它取得之後會再回覆一串 JSON 的字串

```php
$bot = new Bot;
$response = $bot->weatherWitAI($messageText);
$resText = $response['_text'];
$resEntities = $response['entities'];
```

一般是長得這樣

```json
{
    "_text": "新竹市的天氣",
    "entities": {
        "location": [
            {
                "suggested": true,
                "confidence": 0.93386,
                "value": "新竹市",
                "type": "value"
            }
        ],
        "intent": [
            {
                "confidence": 1,
                "value": "temp"
            }
        ]
    },
    "msg_id": "1ls4a4MZWNBLva84C"
}
```

#### 氣象局的資料，這就用 3 天的資料爲主

因爲我用的來源是只有 3 天的資料，變成比較簡便的格式

氣象局的資料有很多，只是拿其中的一個來用而已

下面直接就跑完了全部

```php
if(!empty($resEntities['greetings'][0]['value'])){
    if($resEntities['greetings'][0]['value'] == true){
        $answer = '你好，有什麼可以協助你？';    
    }
}

if(!empty($resEntities['location'][0]['value'])){
    $answer =  $resEntities['location'][0]['value'];
}

// city 
$city_array = [
    "宜蘭縣", "花蓮縣", "臺東縣",
    "澎湖縣", "金門縣", "連江縣",
    "臺北市", "新北市", "桃園市",
    "臺中市", "臺南市", "高雄市",
    "基隆市", "新竹縣", "新竹市",
    "苗栗縣", "彰化縣", "南投縣",
    "雲林縣", "嘉義縣", "嘉義市",
    "屏東縣", "台南市", "台東縣", "台中市", "台北市"
];

$city = '';

foreach($city_array as $val){
    if(strpos($resText, mb_substr($val, 0, 2)) !== false) {
        $city = $val;
    }
}

if(!empty($city) || strpos($resText, '天氣') !== false || strpos($resText, '氣溫') !== false){
    // temp 
    require_once('temp_calcu.php');
}

if(!empty($resEntities['default_city'][0]['value'])){
    $answer = '您好，你預設的地點是新竹市';
}
if(!empty($resEntities['do_what'][0]['value'])){
    $answer = "您好，這是聊天機器人，負責天氣預報，你可以問我「天氣如何？」、「新竹市天氣怎麼樣」之類的話題";
}


if(empty($answer)){
    $answer = '您好，我不太瞭解您所提出的問題！';
}


$response = [
    'recipient' => [ 'id' => $senderId ],
    'message' => [ 'text' => $answer ]
];

$messageToBot = new MessageToBot;
$fbMessage = $messageToBot->FB($accessToken, $response);
```

覺得寫的過程還算簡單，這個 FB Messanger 也複製相同的一份，內容稍微修改就好了