Если мы хотим пробросить stacktrace наверх, то в catch мы должны использовать throw. Если мы хотим пробросить наверх кастомное исключение, например, то мы делаем throw new CustomException(string message) и stacktrace теряется. Чтобы не терять его и передавать кастомного ислкючение мы должны использовать throw new CustomException(string message, Exception oldException).

Использование исключений для логики программы - это антипаттерн т.к. сами исключения дороги потому что собирают stack trace.

Блок finally выпоняет абсолютно всегда и если в нём есть return он перезаписывает предидущий return. Если в нём есть вызов exception, то он перезаписывает другие exceptions.

Часто создают свои кастомыне exceptions для использование в какой - то логике, которая завязана на типах исключений. Кроме того в custom exceptions передают аттрибуты, которые доступны через ex.ShortFall как в данном случае:

public class InsufficientFundsException : Exception
{
    public decimal Shortfall { get; }

    public InsufficientFundsException(decimal shortfall)
        : base($"Insufficient funds, short by {shortfall}")
    {
        Shortfall = shortfall;
    }
}

Мы также должны вызывать родительский конструктор если хотим передать сообщение как в примере выше.