
```python
import csv
from datetime import datetime

class Task:
    def __init__(self, task_id, description, status, owners, due_date, duration):
        self.task_id = task_id
        self.set_description(description)
        self.status = status
        self.owners = owners
        self.due_date = datetime.strptime(due_date, "%Y-%m-%d")
        self.duration = duration

    def set_description(self, description):
        if len(description.split()) > 50:
            raise ValueError("Description must be limited to 50 words.")
        self.description = description

    def change_status(self, status):
        self.status = status

    def change_owners(self, owners):
        self.owners = owners

    def change_due_date(self, due_date):
        self.due_date = datetime.strptime(due_date, "%Y-%m-%d")

    def is_overdue(self):
        return datetime.now() > self.due_date

    def write_description_to_file(self, file_path):
        with open(file_path, 'w') as file:
            file.write(self.description)

class TodoList:
    def __init__(self, name, department):
        self.name = name
        self.department = department
        self.tasks = []

    def add_task(self, task):
        if not isinstance(task, Task):
            raise TypeError("Must add an instance of Task")
        self.tasks.append(task)

    def delete_task(self, task_id):
        self.tasks = [task for task in self.tasks if task.task_id != task_id]

    def get_tasks_by_status(self, status):
        return [task for task in self.tasks if task.status == status]

    def get_task_with_smallest_duration(self):
        return min(self.tasks, key=lambda task: task.duration, default=None)

    def get_task_with_biggest_duration(self):
        return max(self.tasks, key=lambda task: task.duration, default=None)

    def get_busiest_person(self):
        owner_durations = {}
        for task in self.tasks:
            for owner in task.owners:
                if owner not in owner_durations:
                    owner_durations[owner] = 0
                owner_durations[owner] += task.duration
        return max(owner_durations, key=owner_durations.get, default=None)

    def get_laziest_person(self):
        owner_durations = {}
        for task in self.tasks:
            for owner in task.owners:
                if owner not in owner_durations:
                    owner_durations[owner] = 0
                owner_durations[owner] += task.duration
        return min(owner_durations, key=owner_durations.get, default=None)

    def read_tasks_from_csv(self, csv_file):
        with open(csv_file, mode='r') as file:
            csv_reader = csv.DictReader(file)
            for row in csv_reader:
                task = Task(
                    task_id=row['id'],
                    description=row['description'],
                    status=row['status'],
                    owners=row['owners'].split(';'),
                    due_date=row['due_date'],
                    duration=int(row['duration'])
                )
                self.add_task(task)

# Demonstration
# Creating an instance of TodoList
todo_list = TodoList("Project X", "Engineering")

# Adding tasks
task1 = Task(1, "Complete the project documentation.", "in progress", ["Alice"], "2024-07-01", 5)
task2 = Task(2, "Develop the login feature.", "completed", ["Bob", "Alice"], "2024-06-20", 10)
task3 = Task(3, "Design the database schema.", "in progress", ["Charlie"], "2024-06-25", 8)
task4 = Task(4, "Fix the authentication bug.", "in progress", ["Bob"], "2024-06-19", 2)

todo_list.add_task(task1)
todo_list.add_task(task2)
todo_list.add_task(task3)
todo_list.add_task(task4)

# Using methods
tasks_in_progress = todo_list.get_tasks_by_status("in progress")
task_with_smallest_duration = todo_list.get_task_with_smallest_duration()
task_with_biggest_duration = todo_list.get_task_with_biggest_duration()
busiest_person = todo_list.get_busiest_person()
laziest_person = todo_list.get_laziest_person()

print("Tasks in progress:", [task.task_id for task in tasks_in_progress])
print("Task with smallest duration:", task_with_smallest_duration.task_id if task_with_smallest_duration else None)
print("Task with biggest duration:", task_with_biggest_duration.task_id if task_with_biggest_duration else None)
print("Busiest person:", busiest_person)
print("Laziest person:", laziest_person)

# Deleting a task
todo_list.delete_task(2)

# Reading tasks from CSV
# todo_list.read_tasks_from_csv('tasks.csv')  # Uncomment and provide a valid CSV file path to test

```



------

### Class Definitions

#### Task Class

The `Task` class represents a single task within the to-do list.

1. **Constructor (`__init__` method):**
   ```python
   class Task:
       def __init__(self, task_id, description, status, owners, due_date, duration):
           self.task_id = task_id
           self.set_description(description)
           self.status = status
           self.owners = owners
           self.due_date = datetime.strptime(due_date, "%Y-%m-%d")
           self.duration = duration
   ```
   - **Parameters:**
     - `task_id`: An identifier for the task.
     - `description`: A string describing the task.
     - `status`: The current status of the task (e.g., "in progress", "completed").
     - `owners`: A list of strings representing the owners of the task.
     - `due_date`: A string representing the due date of the task in the format "YYYY-MM-DD".
     - `duration`: An integer representing the duration of the task in hours.
   - **Logic:**
     - The constructor initializes the task with the provided `task_id`, `description`, `status`, `owners`, `due_date`, and `duration`.
     - The `description` is set using the `set_description` method.
     - The `due_date` is converted from a string to a `datetime` object.

2. **Method `set_description`:**
   ```python
   def set_description(self, description):
       if len(description.split()) > 50:
           raise ValueError("Description must be limited to 50 words.")
       self.description = description
   ```
   - **Parameters:**
     - `description`: The task description.
   - **Logic:**
     - The method checks if the description has more than 50 words. If it does, it raises a `ValueError`.
     - If the description is valid, it sets `self.description` to the provided description.

3. **Method `change_status`:**
   ```python
   def change_status(self, status):
       self.status = status
   ```
   - **Parameters:**
     - `status`: The new status for the task.
   - **Logic:**
     - The method changes the task's status to the provided `status`.

4. **Method `change_owners`:**
   ```python
   def change_owners(self, owners):
       self.owners = owners
   ```
   - **Parameters:**
     - `owners`: A list of new owners for the task.
   - **Logic:**
     - The method updates the task's owners to the provided list of `owners`.

5. **Method `change_due_date`:**
   ```python
   def change_due_date(self, due_date):
       self.due_date = datetime.strptime(due_date, "%Y-%m-%d")
   ```
   - **Parameters:**
     - `due_date`: The new due date in the format "YYYY-MM-DD".
   - **Logic:**
     - The method changes the task's due date to the provided `due_date`, converting it to a `datetime` object.

6. **Method `is_overdue`:**
   ```python
   def is_overdue(self):
       return datetime.now() > self.due_date
   ```
   - **Logic:**
     - The method checks if the current date and time is later than the task's due date.
     - It returns `True` if the task is overdue and `False` otherwise.

7. **Method `write_description_to_file`:**
   ```python
   def write_description_to_file(self, file_path):
       with open(file_path, 'w') as file:
           file.write(self.description)
   ```
   - **Parameters:**
     - `file_path`: The path to the file where the description should be written.
   - **Logic:**
     - The method opens the specified file in write mode and writes the task's description to it.

#### TodoList Class

The `TodoList` class represents a collection of tasks.

1. **Constructor (`__init__` method):**
   ```python
   class TodoList:
       def __init__(self, name, department):
           self.name = name
           self.department = department
           self.tasks = []
   ```
   - **Parameters:**
     - `name`: The name of the to-do list.
     - `department`: The department to which the to-do list belongs.
   - **Logic:**
     - The constructor initializes the to-do list with the provided `name` and `department`.
     - It also initializes an empty list `self.tasks` to store tasks.

2. **Method `add_task`:**
   ```python
   def add_task(self, task):
       if not isinstance(task, Task):
           raise TypeError("Must add an instance of Task")
       self.tasks.append(task)
   ```
   - **Parameters:**
     - `task`: An instance of the `Task` class.
   - **Logic:**
     - The method checks if the provided `task` is an instance of the `Task` class. If not, it raises a `TypeError`.
     - If the task is valid, it adds the task to the `self.tasks` list.

3. **Method `delete_task`:**
   ```python
   def delete_task(self, task_id):
       self.tasks = [task for task in self.tasks if task.task_id != task_id]
   ```
   - **Parameters:**
     - `task_id`: The identifier of the task to be deleted.
   - **Logic:**
     - The method removes the task with the specified `task_id` from the `self.tasks` list.

4. **Method `get_tasks_by_status`:**
   ```python
   def get_tasks_by_status(self, status):
       return [task for task in self.tasks if task.status == status]
   ```
   - **Parameters:**
     - `status`: The status to filter tasks by.
   - **Logic:**
     - The method returns a list of tasks that have the specified status.

5. **Method `get_task_with_smallest_duration`:**
   ```python
   def get_task_with_smallest_duration(self):
       return min(self.tasks, key=lambda task: task.duration, default=None)
   ```
   - **Logic:**
     - The method returns the task with the smallest duration. If there are no tasks, it returns `None`.

6. **Method `get_task_with_biggest_duration`:**
   ```python
   def get_task_with_biggest_duration(self):
       return max(self.tasks, key=lambda task: task.duration, default=None)
   ```
   - **Logic:**
     - The method returns the task with the biggest duration. If there are no tasks, it returns `None`.

7. **Method `get_busiest_person`:**
   ```python
   def get_busiest_person(self):
       owner_durations = {}
       for task in self.tasks:
           for owner in task.owners:
               if owner not in owner_durations:
                   owner_durations[owner] = 0
               owner_durations[owner] += task.duration
       return max(owner_durations, key=owner_durations.get, default=None)
   ```
   - **Logic:**
     - The method calculates the total duration of tasks for each owner.
     - It returns the owner with the highest total task duration.

8. **Method `get_laziest_person`:**
   ```python
   def get_laziest_person(self):
       owner_durations = {}
       for task in self.tasks:
           for owner in task.owners:
               if owner not in owner_durations:
                   owner_durations[owner] = 0
               owner_durations[owner] += task.duration
       return min(owner_durations, key=owner_durations.get, default=None)
   ```
   - **Logic:**
     - The method calculates the total duration of tasks for each owner.
     - It returns the owner with the lowest total task duration.

9. **Method `read_tasks_from_csv`:**
   ```python
   def read_tasks_from_csv(self, csv_file):
       with open(csv_file, mode='r') as file:
           csv_reader = csv.DictReader(file)
           for row in csv_reader:
               task = Task(
                   task_id=row['id'],
                   description=row['description'],
                   status=row['status'],
                   owners=row['owners'].split(';'),
                   due_date=row['due_date'],
                   duration=int(row['duration'])
               )
               self.add_task(task)
   ```
   - **Parameters:**
     - `csv_file`: The path to the CSV file containing task data.
   - **Logic:**
     - The method reads tasks from a CSV file and adds them to the to-do list.
     - It uses the `csv.DictReader` to read each row as a dictionary and creates a `Task` instance from the row data.
     - Each task is then added to the `self.tasks` list using the `add_task` method.

### Demonstration

1. **Creating an instance of `TodoList`:**
   ```python
   todo_list = TodoList("Project X", "Engineering")
   ```
   - This creates a `TodoList` instance named "Project X" for the "Engineering" department.

2. **Adding tasks:**
   ```python
   task1 = Task(1, "Complete the project documentation.", "in progress", ["Alice"], "2024-07-01", 5)
   task2 = Task(2, "Develop the login feature.", "completed", ["Bob", "Alice"], "2024-06-20", 10)
  

 task3 = Task(3, "Design the database schema.", "in progress", ["Charlie"], "2024-06-25", 8)
   task4 = Task(4, "Fix the authentication bug.", "in progress", ["Bob"], "2024-06-19", 2)

   todo_list.add_task(task1)
   todo_list.add_task(task2)
   todo_list.add_task(task3)
   todo_list.add_task(task4)
   ```
   - Four `Task` instances are created with various attributes.
   - These tasks are added to the `todo_list` using the `add_task` method.

3. **Using methods:**
   ```python
   tasks_in_progress = todo_list.get_tasks_by_status("in progress")
   task_with_smallest_duration = todo_list.get_task_with_smallest_duration()
   task_with_biggest_duration = todo_list.get_task_with_biggest_duration()
   busiest_person = todo_list.get_busiest_person()
   laziest_person = todo_list.get_laziest_person()
   ```
   - `get_tasks_by_status("in progress")` retrieves tasks that are in progress.
   - `get_task_with_smallest_duration()` retrieves the task with the smallest duration.
   - `get_task_with_biggest_duration()` retrieves the task with the biggest duration.
   - `get_busiest_person()` retrieves the owner with the highest total task duration.
   - `get_laziest_person()` retrieves the owner with the lowest total task duration.

4. **Printing the results:**
   ```python
   print("Tasks in progress:", [task.task_id for task in tasks_in_progress])
   print("Task with smallest duration:", task_with_smallest_duration.task_id if task_with_smallest_duration else None)
   print("Task with biggest duration:", task_with_biggest_duration.task_id if task_with_biggest_duration else None)
   print("Busiest person:", busiest_person)
   print("Laziest person:", laziest_person)
   ```
   - The code prints the task IDs of tasks in progress, the task ID of the task with the smallest duration, the task ID of the task with the biggest duration, the busiest person, and the laziest person.

5. **Deleting a task:**
   ```python
   todo_list.delete_task(2)
   ```
   - This deletes the task with `task_id` 2 from the `todo_list`.

6. **Reading tasks from CSV:**
   ```python
   # todo_list.read_tasks_from_csv('tasks.csv')  # Uncomment and provide a valid CSV file path to test
   ```
   - The method `read_tasks_from_csv` can be used to read tasks from a CSV file and add them to the `todo_list`.

### Summary

The provided code defines two classes, `Task` and `TodoList`, to manage tasks in a to-do list. The `Task` class represents individual tasks with attributes like `task_id`, `description`, `status`, `owners`, `due_date`, and `duration`. It provides methods to modify these attributes and check if the task is overdue. The `TodoList` class manages a collection of tasks, providing methods to add, delete, and retrieve tasks based on various criteria, as well as read tasks from a CSV file. The demonstration part of the code shows how to create tasks, add them to a to-do list, and use the methods provided by the `TodoList` class to manage and retrieve task information.