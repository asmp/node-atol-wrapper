[![Build Status](https://travis-ci.com/farafonov-alexey/node-atol-wrapper.svg?branch=master)](https://app.travis-ci.com/farafonov-alexey/node-atol-wrapper)

Название
=========
Node.js обертка для драйвера торгового оборудования (ДТО) версии [10.10.7.0](https://fs.atol.ru/_layouts/15/atol.templates/Handlers/FileHandler.ashx?guid=32a1c76d-1166-4536-b8df-d9cc244f3ff2&webUrl=) от компании [АТОЛ](https://www.atol.ru/)

[ Источник ](https://forum.atol.ru/lofiversion/index.php/t34345.html)

[Документация по интеграции](http://integration.atol.ru/)

Примечание к релизу
========
Обратите внимание: Начиная с версии 10.10.3.0 АТОЛ прекратил прошивку старых версий ККМ (платформа 2.5).
Данный релиз исправляет ошибки компиляции проекта в среде Node.js >=16.
Внесены следущие изменения относительно исходного проекта [node-atol-wrapper](https://github.com/farafonov-alexey/node-atol-wrapper):
- обновлена версия @mapbox/node-pre-gyp
- обновлена версия nan
- обновлена версия node >=16.0.0
- файлы ДТО 10.10.7.0 (поддержка НДС 5%, 7%, тэги для онлайн расчетов 1125 и безналичных платежей)


Важно!!!
- поддерживаются ККМ прошитые на новую платформу 5
- данный релиз не тестировался, предоставляется "как есть", изменения делались "под себя".


Функции
========
Реализованы следующие методы работы с драйвером
* `create()` -  Инициализация драйвера 
* `destroy()` - Деинициализация драйвера
* `getSettings()` - Выгрузка настроек

Возвращает json-объект настроек, который затем можно передать в функцию setSettings
```js
{ AccessPassword: '',
  AutoDisableBluetooth: false,
  AutoEnableBluetooth: true,
  BaudRate: 115200,
  Bits: 8,
  ComFile: '1',
  IPAddress: '192.168.1.10',
  IPPort: 5555,
  LibraryPath: '',
  MACAddress: 'FF:FF:FF:FF:FF:FF',
  Model: 500,
  OfdChannel: 0,
  Parity: 0,
  Port: 0,
  StopBits: 0,
  UsbDevicePath: 'auto',
  UserPassword: '' }
```
* `setSettings(settings)` - Настройка драйвера

Принимает _settings_ json-объект настроек полученных на предыдущем шаге и модифицированых по необходимости.
* `open()` - Соединение с ККТ
* `close()` - Завершение соединения с ККТ
* `processJson(task)` - Выполнение JSON-задания. Синхронная версия функции.

Здесь _task_ - json-объект описание задания для ККТ. Например, задание для открытия смены:
```js
{
    type: 'openShift',
    operator: {
       name: 'Иванов',
       vatin: '123654789507'
    }
}
```
Более подробную информацию по видам json-задании можно получить из документации к [ДТО 10](http://integration.atol.ru/)
* `processJsonAsync(task, callback)`(от [@maxvgi](https://github.com/maxvgi/node-atol-wrapper)) - Выполнение JSON-задания асинхронно. 
После завершения вызывает функцию callback в порядке error-first. 
Вызывать функцию `close()` необходимо только после завершения всех асинхронных заданий.
#### Обработка ошибок
При возникновении ошибки во время выполнения функций ДТО, она обрабатывается и выбрасывается Error c текстом ошибки, 
например, `Error: Ошибка - 4 [ Порт недоступен ]`. При работе с асинхронными функциями объекты Error возвращаются
первым аргументом при вызове функции callback.

### Поддерживаемые платформы:
* Widows, Linux, MacOS

Пример использования
========
Пример использования обертки можно посмотреть в файле index.js
