---
title: 使用 Lumen 建立你的 Facebook Messanger 聊天機器人
categories:
- Laravel
excerpt: |
  使用 Lumen 建立一個屬於你的 Facebook Messanger 的機器人，所有的回答都要看你如何做了，它實際是回應機器人，只要有問，它一定就有回應，中間的做法看你是用 wit.ai 或者 dialogflow 都可以，做法可以有很多，至少不會太單調
feature_text: |
  # Lumen 建立一個 Facebook Messenger 機器人
  別忘了 header 要給它送出喔
feature_image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/milwaukee_1920x1278.jpg" 
image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/facebook_api/1_ADfT0WNAGLxu0wv1IGqOTQ.png"  
---

使用 Lumen 建立一個屬於你的 Facebook Messanger 的機器人，所有的回答都要看你如何做了，它實際是回應機器人，只要有問，它一定就有回應，中間的做法看你是用 wit.ai 或者 dialogflow 都可以，做法可以有很多，至少不會太單調


<!-- more -->

### 這裏我使用 Lumen，這裏可以找到它的資訊

* 5.8 與 5.2 的版本有所不同，注意只有路由不太一樣
* 會選擇它的原因是它本身就沒有前端的界面

[Lumen 5.8 的資訊](https://lumen.laravel.com/docs/5.8)

### 因爲使用 DB 的功能，必須先把它的註解拿掉

* 開啓 bootstrap/app.php
* 找到 // $app->withFacades();，將前面的註解拿掉就好了

### 建立你的路由 ( Route )

它的路徑是 routes/web.php

1. 第一個用 get，主要是給 messanger 的驗證而已
2. 第二個用 post，這個是給傳遞訊息給 messanger server

```php
$router->get('/webhook', 'BotController@verify_token');
$router->post('/webhook', 'BotController@handle_query');
```

### 建立一個 Controller

這個就已經將基本的流程做出來了，整個 copy 吧

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class BotController extends Controller {

    /**
     * The verification token for Facebook
     *
     * @var string
     */
    protected $token;

    public function __construct()
    {
        $this->token = env('BOT_VERIFY_TOKEN');
    }

    /**
     * Verify the token from Messenger. This helps verify your bot.
     *
     * @param  Request $request
     * @return \Illuminate\Http\Response|\Laravel\Lumen\Http\ResponseFactory
     */
    public function verify_token(Request $request)
    {
        $mode  = $request->get('hub_mode');
        $token = $request->get('hub_verify_token');

        if ($mode === "subscribe" && $this->token and $token === $this->token) {
            return response($request->get('hub_challenge'));
        }

        return response("Invalid token!", 400);
    }

    /**
     * Handle the query sent to the bot.
     *
     * @param Request $request
     * @return \Illuminate\Http\Response|\Laravel\Lumen\Http\ResponseFactory
     */
    public function handle_query(Request $request)
    {
        $entry = $request->get('entry');

        $sender  = array_get($entry, '0.messaging.0.sender.id');
        // $message = array_get($entry, '0.messaging.0.message.text');

        $this->dispatchResponse($sender, 'Hello world. You can customise my response.');

        return response('', 200);
    }

    /**
     * Post a message to the Facebook messenger API.
     *
     * @param  integer $id
     * @param  string  $response
     * @return bool
     */
    protected function dispatchResponse($id, $response)
    {
        $access_token = env('BOT_PAGE_ACCESS_TOKEN');
        $url = "https://graph.facebook.com/v2.6/me/messages?access_token={$access_token}";

        $data = json_encode([
            'recipient' => ['id' => $id],
            'message'   => ['text' => $response]
        ]);

        $ch = curl_init($url);
        curl_setopt($ch, CURLOPT_POST, 1);
        curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
        curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
        $result = curl_exec($ch);
        curl_close($ch);

        return $result;
    }
}
```

### 最後就是修改 .env

注意這兩行
* BOT_VERIFY_TOKEN="這裏是放驗證要用到的字串"
* BOT_PAGE_ACCESS_TOKEN="這裏是要放 Messenger 選擇的粉絲團的存取權杖"

```text
APP_ENV=local
APP_DEBUG=true
APP_KEY=
APP_TIMEZONE=UTC

BOT_VERIFY_TOKEN="INSERT_TOKEN_HERE"
BOT_PAGE_ACCESS_TOKEN="INSERT_ACCESS_TOKEN_HERE"

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret

CACHE_DRIVER=file
QUEUE_DRIVER=sync
```

### 這樣你就可以有 Facebook Messanger 的聊天機器人了 