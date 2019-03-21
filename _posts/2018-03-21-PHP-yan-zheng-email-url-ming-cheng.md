---
title: PHP 驗證，這裏使用的是正規表示法
categories:
- PHP
excerpt: |
  PHP 驗證 Email、URL 以及名稱，這裏使用的是正規表示法
feature_text: |
  # PHP 驗證 Email、URL 以及名稱
  
feature_image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/milwaukee_1920x1278.jpg" 
image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/php/php1.jpg"  
---

PHP 驗證 Email、URL 以及名稱，這裏使用的是正規表示法以及 filter_var 來達成驗證


<!-- more -->

### PHP - 驗證名稱

下面的代碼顯示了一個簡單的方法來檢查，如果名稱字段只包含字母和空格。 如果名稱字段的值是無效的，那麼儲存的錯誤信息：

```php
$name = test_input($_POST["name"]);

if (!preg_match("/^[a-zA-Z ]*$/",$name)) {
  $nameErr = "Only letters and white space allowed"; 
}
```

* preg_match() 函數查找字符串的模式，返回 **true** 如果模式存在，否則為 **false**。

### PHP - 驗證電子郵件

檢查是否電子郵件地址以及形成的最簡單，最安全的方法是使用 PHP 的 filter_var() 函數。

在下面的代碼，如果不能很好地形成的電子郵件地址，然後將其存儲的錯誤消息：

```php
$email = test_input($_POST["email"]);

if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
  $emailErr = "Invalid email format"; 
}
```

### PHP - 驗證URL

下面的代碼顯示了一種方法來檢查，如果URL地址語法是否有效。

如果URL地址語法無效，那麼存儲的錯誤信息：

```php
$website = test_input($_POST["website"]) ;

if (! preg_match("/\b(?:(?:https?|ftp) :\/\/|www\.)[-a-z0-9+&@#\/%?=~_|!:,.;]*[-a-z0-9+&@#\/%=~_|]/i",$website)) {
  $websiteErr = "Invalid URL"; 
}
```