

class Task
{
    public int ID { get; }
    public DateTime DueDate { get; }
    public TaskStatus Status { get; set; }

    public Task(int id, DateTime dueDate)
    {
        ID = id;
        DueDate = dueDate;
        Status = TaskStatus.ToDo;
    }
}

enum TaskStatus { ToDo, InProgress, Completed }

class TaskManager
{
    private LinkedList<Task> _toDoList = new();
    private LinkedList<Task> _inProgressList = new();
    private LinkedList<Task> _completedList = new();

    public void AddTask(int id, DateTime dueDate)
    {
        var newTask = new Task(id, dueDate);
        var node = _toDoList.First;

        while (node != null && node.Value.DueDate < dueDate)
            node = node.Next;

        if (node != null) _toDoList.AddBefore(node, newTask);
        else _toDoList.AddLast(newTask);
    }

    public void MoveToInProgress()
    {
        if (_toDoList.Count == 0) return;
        var task = _toDoList.First.Value;
        _toDoList.RemoveFirst();
        task.Status = TaskStatus.InProgress;
        _inProgressList.AddFirst(task); // Stack behavior
    }

    public void MoveToCompleted()
    {
        if (_inProgressList.Count == 0) return;
        var task = _inProgressList.First.Value;
        _inProgressList.RemoveFirst();
        task.Status = TaskStatus.Completed;
        _completedList.AddLast(task); // Queue behavior
    }

    public void DisplayTasks()
    {
        Console.WriteLine("\nTasks:");
        PrintList("To-Do", _toDoList);
        PrintList("In-Progress", _inProgressList);
        PrintList("Completed", _completedList);
    }

    private void PrintList(string title, LinkedList<Task> list)
    {
        Console.WriteLine($"{title}:");
        foreach (var task in list) Console.WriteLine($"Task [{task.ID}] - {task.Status}");
    }
}

class Program
{
    static void Main()
    {
        var manager = new TaskManager();

        manager.AddTask(1, DateTime.Now.AddDays(1));
        manager.AddTask(2, DateTime.Now);
        manager.AddTask(3, DateTime.Now.AddDays(2));

        manager.DisplayTasks();

        Console.WriteLine("\nMoving task to InProgress...");
        manager.MoveToInProgress();
        manager.DisplayTasks();

        Console.WriteLine("\nMoving task to Completed...");
        manager.MoveToCompleted();
        manager.DisplayTasks();
    }
}
