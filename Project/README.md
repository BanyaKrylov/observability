Развернул на базе GAP стека + Loki мониторинг, алертниг и логирование приложения на GO.

На видео показал как это функционирует вживую.

Прикладываю кофниги которые получились по итогу.

На видео показал:
1. Дашборд на котором blakbox стучится на порты:
   - UI
   - Порт авторизации агента
   - Порт управления агентом
   - Порт посгри
   - Порт зукипера
   - Корт кафки
2. GOшные метрики
3. Дашборд кафки
4. Общий системный дашборд
5. Дашборд посгри
6. Дашборд статуса systemd units
7. Дашборд с логами посгри
8. Дашборд с логами кафки
9. Дашборд с логами приложения
10. Далее в UI открыл агента и запустил Live консоль которая пишет в логи сервера и кафки создавая свой топик сессии
11. Показал что логи в кафке и в сервере пишутся где видно что создается новый топик
12. Отправил тестовую строчку в Live консоль чтобы увидеть ее в логах
13. Показал что тестовая строка отобразилась в логах приложения
14. Показал настройки алертов
15. Отключил службу агента
16. Отключил службу кафки
17. Прилетел алерт на то что агент отключен
18. Показал что порт кафки перестал быть доступен
19. Показал логи кафки что она отключилась
20. Показал логи приложения что не может до кафки подключиться
21. Запустил службу агента
22. Отключил службу приложения
23. Прилетело уведомление что алерт по падению агента решен
24. Показал как на дашборде отключаются поочереди порты сервера (3 штуки)
25. Показал логи приложения где видно что оно остановлено
26. Попытался включить сервер без кафки чтобы увидеть ошибку в логах что сервер без кафки не стартует
27. Дождался алерта о том что приложение не запущено
28. Показал логи сервера с ошибкой на дашборде и на сервере