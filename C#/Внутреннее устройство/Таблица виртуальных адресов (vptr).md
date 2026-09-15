
Если метод virtual то в рантайме происходит вычисление метода, который нужно вызывать. Vptr читается только тогда когда мы не знаем какой метод вызвать. У каждого объекта класса есть таблица виртуальных методов, где в переменной _vptr  хранится метод, который нужно вызвать. Если есть override то метод с базового меняется на overrirde.

Таблица vptr используется всегда когда происходит косвенный вызов метода, т.е. на этапе компиляции мы не знаем какой метод нам надо вызывать. Это может быть если в коде мы используем интерфейсы.

Если на этапе компиляции программа уже знает какой метод какого класса вызывать, то вызов работает быстрее.

Пример с Vptr:
```
class Program

static void Main(string[] args)

{

	var childObj = new ChildClass();vptr прошит как ChildClass навсегда
	
	childObj.Method1();переменная = ChildClass, не virtual → компилятор знает сразу// "Child"

	childObj.Method2();переменная = ChildClass, не virtual → компилятор знает сразу// "Child"

	LogInfo(childObj);передаём в BaseClass — переменная меняется, объект в памяти нет

}

static void LogInfo(BaseClass obj)
{
	obj.Method1();virtual → читает vptr → vtable ChildClass → ChildClass::Method1// "Child"

	obj.Method2();не virtual → тип переменной = BaseClass → BaseClass::Method2// "Base"

}

public abstract class BaseClass
{
	public virtual void Method1()virtual → слот в vtable, наследник может переписать
	{ 
		Console.WriteLine("Base"); 
	}
	
	public void Method2()не virtual → vtable не участвует
	{ 
		Console.WriteLine("Base"); 
	}
}

public class ChildClass : BaseClass
{
	public override void Method1()override → переписывает слот в vtable на ChildClass::Method1
	{ 
		Console.WriteLine("Child"); 
	}
	
	public void Method2()не override → vtable не трогает, просто новый метод
	{ 
		Console.WriteLine("Child"); 
	}
}
```