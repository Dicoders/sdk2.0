# Рабочий форк SDK2.0 для сервиса интеграции СДЭК 📦
[![Packagist](https://img.shields.io/packagist/v/ttatpuot/cdek-sdk2.0)](https://packagist.org/packages/ttatpuot/cdek-sdk2.0)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/TTATPuOT/cdek-sdk2.0/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/TTATPuOT/cdek-sdk2.0/?branch=master)
[![Code Coverage](https://scrutinizer-ci.com/g/TTATPuOT/cdek-sdk2.0/badges/coverage.png?b=master)](https://scrutinizer-ci.com/g/TTATPuOT/cdek-sdk2.0/?branch=master)
![License](https://img.shields.io/github/license/ttatpuot/cdek-sdk2.0)

Это рабочий и (пока что) поддерживаемый форк [cdek-it/sdk2.0](https://github.com/cdek-it/sdk2.0). Если что-то не работает или работает не так, как ожидалось, не стесняйтесь [создавать тикеты](https://github.com/TTATPuOT/cdek-sdk2.0/issues).

На данный момент исправлены трудности оригинальной библиотеки:
- Добавлена поддержка PHP 8.0+
- Исправлены мелкие PHPDoc неточности для более грамотных подсказок IDE
- Улучшены и добавлены новые юнит-тесты
- Добавлен метод `Client::calculator()` для расчёта стоимости доставки [по доступным тарифам](https://api-docs.cdek.ru/63345519.html) и [по коду тарифа](https://api-docs.cdek.ru/63345430.html)
- Улучшена [документация](docs/index.md)
- Исправлены тикеты оригинальной библиотеки:
  - [Новые значения фильтра в экшене офисов #20](https://github.com/cdek-it/sdk2.0/issues/20)
  - [При ошибках в запросе создания заказа не заполняется массив errorы у класса ApiReponse #11](https://github.com/cdek-it/sdk2.0/issues/11)

Так же доступны все функции оригинальной библиотеки. Так как это форк, в своём проекте можно просто заменить зависимость на `ttatpuot/cdek-sdk2.0` и актуальную версию. Обратная совместимость сохранится.

---

Реализация API v2.0 для [интеграции со службой доставки СДЭК](https://www.cdek.ru/clients/integrator.html).
Данная SDK поможет быстрее внедрить в свой проект взаимодействие со службой доставки СДЭК.

Возможности SDK:
 
- создание заказа
- информация о заказе
- удаление заказа
- подписка на вебхуки (статусы заказов и статусы печатных форм)
- информация о подписке на вебхуки
- удаление подписки на вебхуки
- получение списка ПВЗ
- создание заявки на вызов курьера
- информация о заявке на вызов курьера
- удаление заявки на вызов курьера
- создание запроса на формирование печатной формы накладной
- создание запроса на формирование печатной формы ШК-места
- получение cписка городов
- получение cписка регионов

Работа со всеми методами API возможна только при наличии доступов к сервису интеграции, которые выдаются только при наличии договора с компанией СДЭК. 

***
### Требования
Требования — минимальны. Нужен PHP 7.1/8.0 или выше.

Данный SDK использует спецификацию [PSR-18 (HTTP-client)](https://www.php-fig.org/psr/psr-18/). 
Это значит в качестве HTTP-клиента можно использовать любой - клиент, поддерживающий данную спецификацию.
Если у вашего клиента нет поддержки этой спецификации, можно посмотреть [имеющиеся адаптеры для большинства популярных HTTP-клиентов](http://docs.php-http.org/en/latest/clients.html)


***
### Установка
Установка осуществляется с помощью менеджера пакетов Composer

```bash
composer require ttatpuot/cdek-sdk2.0
```


***
### Документация

[Полная документация располагается тут](docs/index.md)


***
### Примеры использования

```php
$client = new Psr18Client();
$cdek = new \CdekSDK2\Client($client);
$cdek->setAccount('account');
$cdek->setSecure('secure');

// создание заказа
$order = \CdekSDK2\BaseTypes\Order::create([...]);
$res = $cdek->orders()->add($order);

if ($res->hasErrors()) {
    // Обрабатываем ошибки
    // Если этого не сделать, в будущем будет ошибка RequestException
    foreach ($res->getErrors() as $error) {
        //считываем ошибки
    }
}
if ($res->isOk()) {
    $cdek_order = $cdek->formatResponse($res, \CdekSDK2\BaseTypes\Order::class);
    // $cdek_order->entity->uuid;
    // Если попытаться обработать ответ без проверки $res->isOk()
    // и при этом в ответе будут ошибки, то здесь выбросится исключение RequestException
}

// получение информации о заказе
$res = $cdek->orders()->get($cdek_order->entity->uuid);
if ($res->isOk()) {
    $cdek_order = $cdek->formatResponse($res, \CdekSDK2\Dto\OrderInfo::class);
}




//получить список офисов
$res = $cdek->offices()->getFiltered(['country_code' => 'kz']);
if ($res->isOk()) {
    $pvzlist = $cdek->formatResponseList($res, \CdekSDK2\Dto\PickupPointList::class);
//    $pvzlist->items;
}
```


### Тесты
Запуск тестов:
``` bash
$ composer test
```


### Лицензия
Данный проект распространяется [под лицензией MIT](LICENSE).
