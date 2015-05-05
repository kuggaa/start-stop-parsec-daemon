# start-stop-parsec-daemon для Astra Linux Special Edition

start-stop-parsec-daemon это пропатченная версия обычного
start-stop-daemon для Astra Linux Special Edition. В отличии
от стандартной версии умеет запускать процессы с привилегиями
PARSEC под любым пользователем.

## Лицензия

Как и оригинальное ПО, распространяется на условиях
стандартной общественной лицензии
[GNU (GPL)](http://www.gnu.org/copyleft/gpl.html) версии 2.

Полный текст лиценции находится в файле COPYING.

Сайт проекта [«Лаборатория 50»](http://лаб50.рф).

## Для чего это нужно

Штатные возможности Astra Linux Special Edition не позволяют
запускать демоны со сменой UID/GID и при этом ставить
привилегии PARSEC. Невозможно, например, запускать немодифицированные
программы с привилегией PRIVSOCK (возможность подключения пользователей
с ненулевой мандатной меткой) с UID/GID отличными от 0 (root).

Столь плачевное положение возможно имеет причиной: а) забывчивость
Русбитеха; б) вера в то, что все привилегированные процессы должны
запускаться от рута. Как бы то ни было, вся обвязка связанная с
запуском процессов
с привилегиями PARSEC, полна тлена и баш-скриптов и порой приводит
к потере работоспособности скриптов запуска.

Посему мы это все решили прекратить и сделать свой start-stop-daemon
с PARSEC привилегиями, но без скриптов.

## Как это работает

Пакет устанавливает команду `start-stop-parsec-daemon`, полностью
совместимую со штатным `start-stop-daemon`, но позволяющую указывать
привилегии PARSEC с помощью параметра `--capability`. Для совместимости
со штатным механизмом Астры privsock, если указана привилегия `0x100`
(PRIVSOCK), то команда дополнительно сверяется с файлом
`/etc/parsec/privsock.conf` на предмет наличия запускаемого бинарника
в оном списке.

В остальном, все как обычно.

## Как это всё прикрутить к собственному проекту?

 1. Добавьте в зависимости вашего пакета с init.d скриптом наш
    пакет (parsec-daemon).

 2. В скрипте используйте `start-stop-parsec-daemon` вместо
    `start-stop-daemon`.

 3. Укажите привилегии с помощью параметра `--capability`.

Рекомендация: если вы хотите таким образом адаптировать сторонние пакеты,
то наилучшим способом будет создание пакета <пакет>-parsec, в котором
будет правильный LSB скрипт запуска. А postints/postrm скрипты будут
отключать стандартный демон и работать с `/etc/parsec/privsock.conf`.

## Пример

Конкретного примера не будет, смотрите нашу
[обвязку RabbitMQ](https://github.com/laboratory50/rabbitmq-server-parsec).