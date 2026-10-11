
Это средство для отмены операций. Для начала нужно создать CancellationTokenSource. Это можно сделать разными способами:
1. new CancellationTokenSource();
2. CancellationTokenSource.CreateLinkedTokenSource(cancellationToken1, cancellationToken2)
3. new CancellationTokenSource(Timespan) - создаёт токен, который отменится через период Timespan 

CancellationTokenSource нужно диспозить т.е. нужно его использовать через using

Далее получаем token - cts.Token и передаём в метод, в котором мы можем прервать управление с помощью токена:
4. Можем передать его дальше, например, в _dbContext.Orders.Count(ct)_;
5. Можем проверить в методе через ct.IsCancellationRequested
6. Можем бросить исключение если токен отменён через cts.Token.ThrowIfCancellationRequested();
7. Можем зарегистрировать коллбэки, который выполнится в момент отмены токена. Коллбэки выполняются синхронно друг за другом (LIFO).