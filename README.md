# HW-10.6

##  Домашнее задание к занятию "10.06. Инцидент-менеджмент"

###  Задание 1

Составьте постмотрем, на основе реального сбоя системы Github в 2018 году.

### Итоговый ПОСТМОТРЕМ:

● Краткое описание инцидента

Со стороны пользователей инциндент проявлялся следующим образом:
На Github при создании новых Issue и записей комментариев к уже существующим — 
запись/комментарий визуально отображаются но после перезагрузки — пропадают.

На нескольких сервисах GitHub.com 
пострадали несколько сетевых разделов и последующим сбоем базы данных

● Предшествующие события

22:52 UTC on October 21, стартавали плановые работы по тех. обслуживанию, с цельюзамены 100G оптический модуль.

● Причина инцидента

Во время отключения оптического линка, Оркестратор (ПО Управления кластерами MySQL) перестроил
топологию баз данных. Во время данной перестройки часть записей не были реплицированы. Так как 
происходил обмен коммуникаций между кластерами с разных побережь, из за расстояния была задержка.

В 22:52 (21.10) по UTC на нескольких сервисах GitHub.com 
пострадали несколько сетевых разделов и последующим сбоем базы данных

● Воздействие

Инцидент повлиял только на метаданные веб-сайтов, 
хранящиеся в базах данных MySQL, такие как Issue и PR

● Обнаружение

2018 October 21 22:54 UTC Система мониторинга начала генирить алёрты о множественных faults

● Реакция

К 23:02 UTC, инженеры первой линии обнаружили что топологии кластеров баз  данных 
в неправильном состоянииe. 

В 23:11 UTC Инциндент координатор был подключен к проблеме и принял решение поднять статус проблемы до красной.

Также инженеры с команды DBA были подключены к проблеме

GitHub опубликовал блог для отражения проблемы, предпринятых действий и времемени восстановления

● Восстановление

План востановления был следующий: восстановление БД из бекапов, синхронизация реплик, востановление топологии кластеров, 
отработко заданий в очередях.

Задержка в обмене данными между ДЦ на разных побережьях, пользовательский трафик с начала
рабочего дня повлияли на скорость востанвления

● Таймлайн

      2018 October 21 22:52 UTC начало работ на 100G optical equipment 
      2018 October 21 22:52 UTC Orchestrator начал процесс выбора мастер кластера 
      2018 October 21 22:54 UTC Система мониторинга начала рассылать алёрты об авариях
      2018 October 21 23:07 UTC Инженеры первой линии были подключены к инцинденту
      2018 October 21 23:13 UTC Стало ясно что проблема затрагивает несколько кластеров, д
      ополнительная команда инженеров была подключена к проблеме
      2018 October 21 23:19 UTC Специалисты деградироваль часть сервисов отображения-юзабилити
      на сайте Гитхаб с целью сохранения данных
      2018 October 22 00:05 UTC Ответственные за инциндент инженеры начали разрабатывать план восстановления
      2018 October 22 00:41 UTC Стартовал процесс восстановления из Бекапа
      2018 October 22 06:51 UTC Несколько кластеров в US East Coast data center были восстановлены и 
      начали replicating новые данные с West Coast.
      2018 October 22 11:12 UTC Все БД в ДЦ US East Coast опять стали в статусе мастер
      2018 October 22 16:24 UTC Реплики были синхронизированы, и оригинальная топология была востановлена
      2018 October 22 16:45 UTC Работы по разбиранию беклога, ручному востановлениею данных, 
      2018 October 22 23:03 UTC Вебхуки и страницы сайта были востановлены и было подтверждено что все системы 
      функционируют штатно.


● Последующие действия

Перенстроить конфигураци Оркестратора, что бы предотвратить процесс взаимодействия кластеров по выбору мастер БД
между разными побережьями US.

Организация архитектуры с резервированием N+1


