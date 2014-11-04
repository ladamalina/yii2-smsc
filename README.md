[Smsc.ru](http://smsc.ru) wrapper for Yii 2 

## Installation

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require --prefer-dist ladamalina/yii2-smsc "*"
```

or add

```
"ladamalina/yii2-smsc": "*"
```

to the require section of your `composer.json` file.

## Usage

To use sender, you should configure it in the application configuration like the following,

```php
'components' => [
	...
	'sms' => [
        'class'    => 'ladamalina\smsc\Smsc',
        'login'     => '',  // login
        'password'   => '', // plain password or lowercase password MD5-hash
        'post' => true, // use http POST method
        'https' => true,    // use secure HTTPS connection
        'charset' => 'utf-8',   // charset: windows-1251, koi8-r or utf-8 (default)
        'debug' => false,    // debug mode
    ],
	...
],
```

## Examples

Обычное сообщение

```php
list($sms_id, $sms_cnt, $cost, $balance)
    = Yii::$app->sms->send_sms('79999999999', 'Ваш код для получения скидки');
/*
    возвращает массив (<id>, <количество sms>, <стоимость>, <баланс>) в случае успешной отправки
    либо массив (<id>, -<код ошибки>) в случае ошибки
*/
```

Проверка результата отправки

```php
$sms = Yii::$app()->sms;
$result = $sms->send_sms('79999999999', 'Ваш код для получения скидки');
if (!$sms->isSuccess($result)) {
    echo $sms->getError($result);
    die();
}
```

Отправка на группу номеров

```php
// в первом параметре передаем список телефонов через запятую или точку с запятой
list($sms_id, $sms_cnt, $cost, $balance)
    = Yii::$app->sms->send_sms('79999999999,79999999990', 'Ваш код для получения скидки');
```

Для перевода сообщения в транслит

```php
// в третьем параметре передаем 1
list($sms_id, $sms_cnt, $cost, $balance)
    = Yii::$app->sms->send_sms('79999999999', 'Вы сегодня неотразимы', 1);
```

Отправка от имени Ivan с отложенным временем доставки

```php
// будет доставлено абоненту 01.01.2012 г. в 00:00
list($sms_id, $sms_cnt, $cost, $balance)
    = Yii::$app->sms->send_sms('79999999999', 'Вы сегодня неотразимы', 0, '0101120000', 0, 0, 'Ivan');
```

Для проверки статуса доставки SMS
```php
list($status, $time) = Yii::$app->sms->get_status($sms_id, '79999999999');
// возвращает массив [ <статус>, <время изменения>, <код ошибки доставки> ]
```

Проверка состояния баланса
```php
$balance = Yii::$app->sms->get_balance();
// возвращает баланс в виде строки или false в случае ошибки
```

Проверка стоимости sms
```php
list($cost, $cnt)
    = Yii::$app->sms->get_sms_cost('79999999999', 'Вы сегодня неотразимы');
// возвращает массив [ <стоимость>, <количество sms> ]
```

