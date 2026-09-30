Оператор, который возвращает информацию по строкам, которые были затронуты INSERT/UPDATE/DELETE операциями, например:

`INSERT INTO shipment (guid, contractor, number, sum, date, messagedate)`
`VALUES ('new-guid', 'contractor-1', 'N-100', 5000, now(), now())`
`RETURNING id, guid, dateupdate;`

По сути это ещё одна атомарная операция как и [[UPSERT]], которая внутри содержит операцию обновления таблицы и SELECT. Для HighLoad это полезно, т.к. исключаем race condition между операциями.