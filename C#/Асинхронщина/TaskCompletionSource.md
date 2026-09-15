Нужен для того , что привести синтаксис старого легаси метода к требуемум формату async/await.

Это надстройка на Task, которая позволяет управлять его завершением вручную. Обычный Task завершается когда сам код метода заканчивается, TaskCompletionSource позволяет оставновить его в любой момент времени через SetResult, SetException, SetCanceled. Главная идея - превратить любой колллбэк в async/await метод.

Старый код работает через колбэки:  GetData(onSuccess: result => ..., onError: ex => ...)

В классе источнике мы создаёт TSC, передаём его в класс приёмник, где проводим действия и в конце делаем либо tcs.SetResult(result) в качестве onSuccess,  tsc.SetException(ex) в качестве onError. Класс источник не пойдёт дальше обрабатывать код пока класс приёмник не вызовет один из этих двух методов.

Пример работы с колбэками:

var downloader = new LegacyDownloader();

downloader.Download("https://example.com", 
    onSuccess: data => 
    {
        Console.WriteLine(data);
        // Здесь может быть еще один вложенный вызов...
    },
    onError: ex => 
    {
        Console.WriteLine($"Ошибка: {ex.Message}");
    });
    
Получилось:

public class ModernDownloader
{
    private readonly LegacyDownloader _legacyDownloader = new();

    // Новый современный метод
    public Task<string> DownloadAsync(string url)
    {
        // 1. Создаем TCS. Он будет представлять собой Task для внешнего мира.
        var tcs = new TaskCompletionSource<string>();

        // 2. Вызываем старый метод, но вместо своих коллбеков передаем 
        // методы самого TCS, которые изменят состояние Task.
        _legacyDownloader.Download(
            url,
            onSuccess: result => tcs.SetResult(result),   // Если успех -> завершаем Task с результатом
            onError: error => tcs.SetException(error)     // Если ошибка -> завершаем Task с исключением
        );

        // 3. Возвращаем Task. Вызывающий код будет ждать его через await.
        return tcs.Task;
    }
}

Т.е. мы в любом случае мы передаём объект, который будет использовать легаси и в который возможно что - то положит. Сам легаси - код будет выполняться в любом случае, даже если мы не будем ожидать (await tcs) того, что легаси что то положит.

TaskCompletionSource(TaskCreationOptions.RunContinuationsAsynchronously);  указывает, что continuation (код после await completion.Task) будут выполняться в другом потоке.

Нужен для  того, чтобы в качестве коллбэков в легаси методе можно передавать коллбэки TSC и возвращаться task с результатами выполнения легаси метода.

Внутри хранится список с методами _continuations, которые нужно выполнить после Task, т.е. те, которые его ожидают.