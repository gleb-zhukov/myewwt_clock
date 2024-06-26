# Функции данного устройства также выполняет реле или розетка, настроенные в режим шлюза. Подробная и актуальная информация [здесь](https://github.com/gleb-zhukov/smart_socket_and_relay).


# Miss You Even When We're Together
Miss You Even When We're Together (MYEWWT) — это основное устройство умного дома.

![IMG_7676](https://user-images.githubusercontent.com/84660518/156761977-2611df78-57b0-47a1-8940-1fd69deb85cf.jpg)

## Содержание
- [MYEWWT](#myewwt)
- [Подробнее о системе и коде](#code)
- [Плата и основные компоненты](#components)
- [Схема и работа печатной платы](#scheme)
- [Запуск и настройка](#usage)
- - [Создание бота в Telegram](#bot)
- - [Настройка MYEWWT](#myewwt_setup)
- [Обновление прошивки](#update)
- [Ответственность](#responsibility)


<a id="myewwt"></a>
# MYEWWT

MYEWWT выполнено в виде настольных часов, сочетающих в себе функции Wi-Fi шлюза, часов реального времени, оснащён датчиком влажности, температуры воздуха а также фоторезистора для адаптивного изменения яркости сегментов часов в зависимости от условий освещённости в помещении. 

MYEWWT взаимодействует c API Telegram, позволяя пользователю отправлять команды и предоставляет на них ответ в Telegram-боте, заренее созданном пользователем. 

MYEWWT подключается к Интернету через WI-Fi и взаимодействует с Telegram ботом, получая команды от него, обрабатывает их и управляет периферийными устройствами: выключателями света, умными розетками и т.д. с помощью технологии [ESP-NOW.](https://www.espressif.com/en/products/software/esp-now/overview)

Возможности устройства MYEWWT:

* соединение с точкой доступа Wi-Fi и выход в интернет,
* обмен данными с другими устройствами умного дома с применением технологии [ESP-NOW](https://www.espressif.com/en/products/software/esp-now/overview),
* контроль температуры и влажности воздуха в помещении,
* возможность смены цвета сегментов часов на любой из диапазона RGB.

![IMG_7698](https://user-images.githubusercontent.com/84660518/156762345-621baf70-fe9c-40f6-bed2-8e6a1df0902c.jpg)


<a id="code"></a>
# Подробнее о системе и коде
Здесь и далее находится описание устройства MYEWWT. Информацию о системе умного дома, особенностях кода прошивки вы можете найти [здесь](https://github.com/gleb-zhukov/smart_home_system).

<a id="components"></a>
# Плата и основные компоненты
Основная рабочая часть устройства — печатная плата. 

Плата спроектирована таким образом, чтобы её лицевая часть находилась снаружи корпуса, и не была чем-либо закрыта, за исключением кнопки сброса всех настроек. 

![Безымянный](https://user-images.githubusercontent.com/84660518/156762616-e82dd26a-b390-45fa-8fc3-ae602864f3ff.png)

Основные компоненты, обеспечивающие функционал и работу MYEWWT:
 * микроконтроллер Espressif ESP-12F,
 * датчик температуры и влажности воздуха DHT-22,
 * адресные RGB светодиоды WS2812b,
 * фоторезистор GL5528.

<a id="scheme"></a>
# Схема и работа печатной платы

![Schematic_MYEWWT v2_2022-03-02](https://user-images.githubusercontent.com/84660518/156762680-4c0737e8-3d79-4e44-b435-4487b4576dfe.png)

На плату подаётся питание 5 В от USB кабеля. Так как микроконтроллеру ESP-12F требуется питание 3.3 В, предусмотрен блок стабилизации напряжения — непосредственно сам стабилизатор AMS1117-3.3, а также входные и выходные сглаживающие конденсаторы для обеспечения стабильности работы платы. 

Светодиодам WS2812b, в отличие от ESP-12f, для корректной работы требуется 5 В, поэтому на плате предусмотрен конвертер логических уровней на основе N-канального MOSFET транзитора. 

Также в схему подключен модуль DHT-22, который обменивается с модулем ESP-12f показаниями температуры и влажности воздуха по линии SDA.

Для обеспечения адаптивной яркости светодиодов в схему включен фоторезистор, подключенный к ADC пину микроконтроллера с применением резисторов, обеспечивающих рабочий диапазон на аналоговом входе микроконтроллера.

<a id="usage"></a>
# Запуск и настройка


<a id="bot"></a>
## Создание бота в Telegram

Чтобы управлять системой умного дома zhukov technologies с помощью устройства MYEWWT необходимо создать собственного бота в Telegram. 

Для этого необходимо: 

1. Открыть диалог с [BotFather](https://t.me/BotFather). Это главный бот - он создаёт ботов. Нажать старт, а после его ответа ввести команду /newbot.
2. Придумать любое название (name) для бота, например 'Мой дом' или 'Квартира 50'.
3. Придумать имя бота (username). Оно должно оканчиваться на '_bot'.
Стоит обратить внимание, имя должно быть уникальное и написано на латинице! 

![bot_setup_photo](https://sun9-16.userapi.com/impg/-nRxiS5cMcZzMEwY4AHRpcTjz620_PIXO3lhCQ/SQggVuj7LbE.jpg?size=643x708&quality=96&sign=7c06548da13f7d55b5b9700b7de150ec&type=album)

После завершения процедуры создания бота, необходимо сохранить выделенные данные — ссылку на бота и токен.

Внимание! Токен обязательно нужно хранить в тайне, так как с помощью него предоставляется доступ к боту.

<a id="myewwt_setup"></a>
## Настройка MYEWWT

Когда бот создан, можно приступить к настройке MYEWWT. 

Для этого необходимо: 

1. Включить MYWWT. Все сегменты загорятся переливающейся радугой. Нужно подключиться с телефона, планшета или компьютера к Wi-Fi сети MYEWWT с паролем iloveyou.
2. Зайти в браузер по адресу http://10.10.10.10 и ввести точку доступа своего домашнего Wi-Fi, пароль и токен созданного бота в предложенную форму.

![myewwt_setup_photo](https://sun9-54.userapi.com/impg/IpzngzNuStnws7ECdrUQCHijwsNiuoEb9tLQ7A/L4JM7qbrJYI.jpg?size=1184x844&quality=96&sign=d294bd7de6687164ecdd1c54ec3a91b0&type=album)

После всех процедур в течении нескольких секунд MYEWWT соединится с сетью, время синхронизируется по NTP серверу и станет доступен весь функционал MYEWWT.

Возможности системы умного дома
- [X] Управление розетками и реле с получением ответа о состоянии
- [X] Получение информации о температуре воздуха от MYEWWT
- [X] Получение информации о влажности воздуха от MYEWWT
- [X] Добавление и удаление из списка розеток и реле
- [X] Возможность настраивать (связывать) выключатель с розеткой и реле
- [X] Управление цветом сегментов часов
- [X] Возможность делегировать другим пользователям права на управление системой
- [X] Автоматическое обновление прошивки

Линейка устройств будет расширяться, возможности системы будут дополняться. 


<a id="update"></a>
# Обновление прошивки

Данный код предполагает развитие системы, совершенствование и создание новых устройств, поэтому в реле и розетках а также в других устройствах zhukov technologies предусмотрена функция обновления прошивки: раз в 12 часов устройство пытается связаться с сервером, где находится файл со следующей версией прошивки.
При отсутствии файла, следующий запрос происходит через 12 часов. Если на сервере присутствует запрашиваемый файл — прошивка обновляется. 

Данная возможность реализована с помощью встроенной в SDK библиотеки ESP8266httpUpdate.

В функции обновления сначала создаётся клиент:
```cpp
WiFiClient client;
```

затем объявляются callback функции:
```cpp
ESPhttpUpdate.onStart(update_started);
ESPhttpUpdate.onEnd(update_finished);
ESPhttpUpdate.onProgress(update_progress);
ESPhttpUpdate.onError(update_error);
```

и непосредственно сама строчка, отвечающая за связь с сервером и обновление прошивки:
```cpp
t_httpUpdate_return ret = ESPhttpUpdate.update(client, "http://www.myserver.ru/myewwt_v2.bin");
```

Функция обновления прошивки справляется с некоторыми внештатными ситуациями и не вызывает поломок и "окирпичивания" модуля: при внезапном отключении питания, обрыве связи с сервером и прочими проблемами прошивка приостанавливается и возобновляется при восстановлении подключения или подключении питания.



<a id="responsibility"></a>
# Ответственность

В прошивке MYEWWT используются библиотеки из проектов с открытым исходным кодом на основании лицензий, закреплёнными за авторами библиотек. Таким образом, работоспособность библиотек не может быть гарантирована. Также нет никаких гарантий, что устройство будет работать с вашей электросетью, и вы не получите ущерба во время эксплуатации устройства.
