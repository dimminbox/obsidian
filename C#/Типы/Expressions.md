
Выражения в C#  - это мета - описания выражений, в т.ч. функций. Описание реализовано с использованием особенных объектов и связей в c# и компилируется в специальное дерево объектов, например, BinaryExpression, ParameterExpression, ConstantExpression и др.

У делегатов тело лямбды свою очередь компилируются напрямую в IL. 

Выражения широко используются в LINQ, SQL - генераторах, рефлексии, динамическая генерация кода.

Для вызова используются следующие методы:

- Activator.CreateInstance(Type) - создаёт объект
- PropertyInfo.SetValue(obj, value) - устанавливает значение в свойство
- MethodInfo.Invoke(obj, values) - вызывает метод

Для построения ExpressionTrees используются следующие методы:
- Expression.Parameter(type) - создаёт описание параметра метода
- Expression.Convert(instanceParam, type) - создаём описание приведения объекта к нужному типу
- Expression.Call(castInstance, method) - создаём описание сигнатуры метода
- Expression.Lambda<Action<object>>(call, instanceParam); - создаём лямбду метода с параметром на базе сигнатуры метода
- lambda.Compile() - получаем скомпилированную функцию
  
Reflection меделенный потому что:
 - type.GetMethod("Greet") — сканирует всю Method Table у объекта
 - MethodInfo.Invoke(obj, values). Параметры передаются как object[] и плэтому мы всё время делаем boxing (упаковку), т.е. сохраняем их в кучу.
 - CLR проверяет совместимость типов в рантайме, например, при SetValue, Invoke
 - нет [[Inlining JIT]]