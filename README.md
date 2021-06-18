# Booking expain

Описать пример технического решения для сервиса бронирования номеров.

## Решение (вариант)
 ### таблички

* `users` - юзеры
* `rooms` - номера
* `orders` (user_id, room_id, start_date, end_date) - заказы (лучше их хранить тоже)
* `reservations` (room_id, date) - резервирования (с уникальным индексом по этим двум полям)

### логика

* Уровень приложения работает только с тремя моделями (`users`, `rooms`, `orders`)
* `Order.create!(user: user, room: room)` - на этом все
* В базе висит тригер, при внесении записи в `orders`, блокируется табличка `reservations` и инсертятся соответствующие записи `room_id`, `date` на каждый день из интервала в заказе (лучше попробовать одним запросом).
* В случае, когда иной заказ будет иметь какое-то пересечение даты и комнаты в `reservations`, вызов `Order.create` закончится неудачей, база вернет ошибку от уникального индекса.

Done.

## PS

Можно на уровне приложения все запилить, без процедур бд, в одной транзакции все сделать. Но, в случае отдельной таблички `orders`, это не есть хорошо, поскольку остаются иные способы внести запись в табличку заказы.
