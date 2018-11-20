---
layout: post
title: Тревожный кактус
date: 2018-11-20 17:00:00
categories: [Cacti, Dev]
---

*Чет я ничего не бложил давно*

![Cacti](/images/cacti.png)

Welcome back! Прошел почти месяц, а постов и след простыл. Но пришла пора исправить эту оплошность. Сегодня мы поговорим о массовом добавлении устройств в Cacti, а также автоматической генерации графиков при добавлении. Поехали!

Нативной поддержки массового добавления устройств, как и во многих системах, в Cacti нет. Зато для кактуса есть специальный cli скрипт, который позволяет добавлять устройства через терминал, минуя интерфейс. Ну а тут уже нет ничего проще, чем написать скрипт для того, чтобы подцепить данные устройств с, например, файла :)

```php

<?php

ini_set('error_reporting', E_ALL);
ini_set('display_errors', 1);
ini_set('display_startup_errors', 1);

echo "<pre>";
$devices_list = "test_devices.txt"; //file with list of ips
$path_to_cli_cacti = "/usr/share/cacti/cli"; //full path to cli directory of cacti
$community_name = "public"; //name of your community of devices
$template_id = 5; //id of template for your $devices
$explode_separator = "	"; //the symbol for separate description and ip


$devices = file($devices_list);

foreach($devices as $device){
  $device_parts = explode($explode_separator, $device);
  $output = shell_exec("php ".$path_to_cli_cacti."/add_device.php --community=".$community_name." --template=".$template_id." --description=".$device_parts[0]." --ip=".$device_parts[1]);
  print_r($output);
}

?>

```

А вот и моя наработка, созданная специально для этих целей. Она подхватывает имя и ip из файла, которые в данном случае разделены табуляцией (*последствия копипаста из LibreOffice Calc*). Написана, как ни странно, на PHP, но можно спокойно на любом другом языке, даже на *баше*. Запустив этот скрипт, придется подождать достаточно длительное время, особенно, если к устройству автоматически что-то дополнительно цепляется (например, те же графики).


