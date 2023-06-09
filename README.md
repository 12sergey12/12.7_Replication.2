# Домашнее задание к занятию «Репликация и масштабирование. Часть 2» Баранов Сергей


---

### Задание 1

### Опишите основные преимущества использования масштабирования методами:

- активный master-сервер и пассивный репликационный slave-сервер; 
- master-сервер и несколько slave-серверов;
- активный сервер со специальным механизмом репликации — distributed replicated block device (DRBD);
- SAN-кластер.

*Дайте ответ в свободной форме.*

1. активный master-сервер и пассивный репликационный slave-сервер:
в этом подходе выделяется один основной сервер базы данных, который называется Мастером. На нем происходят все изменения в данных. Слейв сервер постоянно копирует все изменения с Мастера. С приложения на Слейв сервер отправляются запросы чтения данных (запросы SELECT). Таким образом Мастер сервер отвечает за изменения данных, а Слейв за чтение.
- Высокая доступность, надежность и отказоустойчивость, так как имеется резервное копирование данных на специальных slave-серверах.
- Увеличенная производительность при запросах на чтение и запись, так как данные на slave-серверах могут быть использованы для балансировки нагрузки.
- В случае отказа master-сервера, пассивный slave-сервер может стать активным и продолжать обслуживание запросов.

2. master-сервер и несколько slave-серверов:
преимущество этого типа репликации в том, что можно использовать более одного Слейва. Обычно следует использовать не более 20 Слейв серверов при работе с одним Мастером.
- Высокая доступность и распределение нагрузки.
- Увеличение производительности при большом количестве запросов на чтение, так как данные на slave-серверах могут быть использованы для балансировки нагрузки.
- Отказоустойчивость, так как в случае сбоя на одном сервере, остальные серверы продолжат работу.

3. активный сервер со специальным механизмом репликации — distributed replicated block device (DRBD):
поддерживает как синхронную, так и асинхронную репликацию. Также поддерживается промежуточный протокол, при котором запись считается успешной, если она завершилась на локальное устройство, и удаленный узел подтвердил получение данных. Синхронизация идёт через протокол TCP.
- Использование блочного устройства, позволяет работать с различными типами файловых систем, включая кластерную файловую систему (например, GFS2 или OCFS2).
- Репликация данных происходит в режиме реального времени, что позволяет минимизировать время простоя в случае сбоя на одном из серверов.
- Высокая доступность, отказоустойчивость и возможность балансировки нагрузки при работе в кластерном режиме.


4. SAN-кластер.
Упрощает администрирование и добавляет изрядную гибкость, поскольку кабели и дисковые массивы не нужно физически транспортировать и перекоммутировать от одного сервера к другому. Другим преимуществом является возможность загружать сервера прямо из сети хранения. Централизованное управление, высокое быстродействие, масштабируемость, отказоустойчивость,
- Высокая производительность и отказоустойчивость благодаря общему доступу к устройствам хранения данных.
- Возможность горячей замены отказавших компонентов.
- Расширяемость и гибкость в настройке системы.


---

### Задание 2


### Разработайте план для выполнения горизонтального и вертикального шаринга базы данных. База данных состоит из трёх таблиц: 

- пользователи, 
- книги, 
- магазины (столбцы произвольно). 

### Опишите принципы построения системы и их разграничение или разбивку между базами данных.

*Пришлите блоксхему, где и что будет располагаться. Опишите, в каких режимах будут работать сервера.*


Горизонтальное масштабирование: То есть в исходной БД одна или несколько таблиц очень объемные и нужно их разделить на части, скорее всего это касается таблицы с книгами, можно разбить на id с 1 по 10000, с 10001 по 20001,с 30001 по 40001 и т.п. В принципе, может понадобиться аналогичная операция с таблицей пользователей по такой же схеме.

Горизонтальное шардинг базы данных:
Разделение данных между физическими серверами.
Каждый сервер хранит данные только для определенного диапазона значений ключей из таблицы.
Использование партиция для разделения данных.
Каждый сервер работает как независимая единица, но использует общую инфраструктуру кластера.

![monitoring](https://github.com/12sergey12/12.7_Replication.2/blob/main/images/%D0%B3%D0%BE%D1%80.%D1%88%D0%B0%D1%80%D0%B4%D0%B8%D0%BD%D0%B3.png)

Вертикальное масштабирование: Мы просто выносим разные таблицы в разные базы данных:

Вертикальный шардинг базы данных:
Разделение данных между физическими серверами.
Каждый сервер хранит только определенное подмножество столбцов из таблицы.
Использование определенной комбинации столбцов для разделения данных.
Каждый сервер работает как независимая единица, но использует общую инфраструктуру кластера.

Режимы работы серверов:
Каждый сервер работает как независимая единица, обеспечивая доступ к своей части данных.
Сервера должны использовать общую инфраструктуру кластера, например, общие ресурсы сети, вычислительных мощностей и хранилища данных.


![monitoring](https://github.com/12sergey12/12.7_Replication.2/blob/main/images/%D0%B2%D0%B5%D1%80%D1%82.%D1%88%D0%B0%D1%80%D0%B4%D0%B8%D0%BD%D0%B3.png)
