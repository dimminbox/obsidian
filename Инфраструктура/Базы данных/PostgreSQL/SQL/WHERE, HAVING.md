
Where выполняется до оператора GROUP BY и поэтому не может использовать результаты группировки в отличие от HAVING, которая может фильтровать уже по сгруппированным данным, например:

SELECT category, AVG(price) AS avg_price
FROM products
WHERE stock > 0
GROUP BY category
HAVING AVG(price) > 100;

