CTE (Common Table Expression) - именнованный результат внутри запроса.

WITH active_shipments AS (
    SELECT id, contractor, sum
    FROM shipment
    WHERE date > '2024-01-01'
)
SELECT contractor, count(*), sum(sum)
FROM active_shipments
GROUP BY contractor;

Мы объявляем active_shipments как алиас на запрос и далее используем его как будто бы это отдельная таблица. При выполнении планировщик встаривает (инлайнинг) этот подзапрос в основной запрос и оптмизирует всё это как одно целое. Эта фича работает с 12 версии Postgres, до этого момента CTE выполнялась как отдельный запрос.

Также есть RECURSIVE WITH - это рекурсивный CTE который вызывает сам себя.