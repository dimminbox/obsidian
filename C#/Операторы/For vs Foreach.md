В C#6 оператор foreach для каждой итерации сохраняет значение в локальную переменную, до 6 версии он этого не делал так же как и for.

`var actions = new List<Action>();` 
`foreach (int i in new[] { 0, 1, 2, 3, 4 })` 
`{` 
	`actions.Add(() => Console.WriteLine(i));` 
`}` 
`foreach (var action in actions)` 
	`action();`

выведет 0, 1, 2, 3, 4 

в то время как for выведет 4 4 4 4 4

