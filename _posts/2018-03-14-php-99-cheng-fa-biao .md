---
title: PHP 的 99 乘法表
categories:
- PHP
excerpt: |
  PHP 的 99 乘法表，對於新手，這是很簡單的事
feature_text: |
  # PHP 的 99 乘法表
  99 乘法表
feature_image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/milwaukee_1920x1278.jpg"  
image: "https://raw.githubusercontent.com/zivhsiao/repo-picture-1/master/images/milwaukee_1920x1278.jpg"  
---

這是前不久，爲了證明我的 codeing 實力，就專門寫 99 乘法表

<!-- more -->

直接就寫吧

```php
for($x = 2; $x <= 9; $x++){
   for($y = 1; $y <= 9; $y++){
      echo "$x * $y =" . ( $x * $y );
      echo "\n";
   }
}
```

得到的結果是：
```
2 * 1 =2
2 * 2 =4
2 * 3 =6
2 * 4 =8
2 * 5 =10
2 * 6 =12
2 * 7 =14
2 * 8 =16
2 * 9 =18
......
.......
........
```

以此類推，一直到 9 * 9 = 81

夠簡單吧