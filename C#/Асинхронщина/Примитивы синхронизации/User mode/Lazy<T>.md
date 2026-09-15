
Это примитив синхронизации, который работает на уровне user mode и инициализирует объект только тогда когда идёт обращение к его свойству и именно поэтому он "ленивый".

Выглядит от примерно так:

public class Lazy<T>
{
    private int _state; // 0=не создан, 1=создаётся, 2=готов — БЕЗ volatile
    private T _value;
    private Func<T> _factory;

    public Lazy(Func<T> factory)
    {
        _factory = factory;
    }

    public T Value
    {
        get
        {
            if (Volatile.Read(ref _state) == 2)
                return _value;

            return CreateValue();
        }
    }

    private T CreateValue()
    {
        if (Interlocked.CompareExchange(ref _state, 1, 0) == 0)
        {
            try
            {
                _value = _factory();
                Volatile.Write(ref _state, 2);
            }
            catch
            {
                Volatile.Write(ref _state, 0);
                throw;
            }
        }
        else
        {
            SpinWait spin = default;
            while (Volatile.Read(ref _state) != 2)
                spin.SpinOnce();
        }

        return _value;
    }
}

Внутри он использует Interlocked для чтения состояния: объект "не создан", "создан", "создаётся".

Он использует Spin lock для ожидания пока другие объекты создают объект. Сделано это так чтобы не переключаться в kernel mode и не тратить время.

Volatile используется для работы с состоянием чтобы 