
Оператор который позволяет задать альтернативное действие если не выполнился insert
Например:

`INSERT INTO shipment (guid, contractor, number, sum, date, messagedate)`
`VALUES ('abc-123', 'contractor-1', 'N-001', 1500, now(), now())`
`ON CONFLICT (guid) DO UPDATE`
`SET sum = EXCLUDED.sum,`dateupdate = now();`

или

`INSERT INTO shipment (guid, contractor, number, sum, date, messagedate)`
`VALUES ('abc-123', 'contractor-1', 'N-001', 1500, now(), now())`
`ON CONFLICT (guid) DO NOTHING;`

Exсluded - это псевдотаблица ,которая содержит значения, которые мы пытались вставить.  Также мы можем прописать сразу несколько столбцов на случай конфликта через запятую в CONFLICT (), тогда альтернативное поведение будет срабатываеть если оба столбца привели к конлфикту.