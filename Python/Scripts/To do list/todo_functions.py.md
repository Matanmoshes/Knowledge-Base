 
 
 ```python
 # todo_functions.py

# Define the to-do list dictionary
to_do_list = {}

# Function to add a new task
def add_task(task_id, description, status, due_date):
    if task_id in to_do_list:
        raise ValueError("Task ID already exists.")
    to_do_list[task_id] = {
        "description": description,
        "status": status,
        "due_date": due_date
    }

# Function to remove a task by its ID
def remove_task(task_id):
    if task_id not in to_do_list:
        raise ValueError("Task ID not found.")
    del to_do_list[task_id]

# Function to update the status of a task by its ID
def update_task_status(task_id, status):
    if task_id not in to_do_list:
        raise ValueError("Task ID not found.")
    to_do_list[task_id]["status"] = status

# Function to list all tasks with their details
def list_tasks():
    for task_id, task_info in to_do_list.items():
        print(f"Task ID: {task_id}")
        print(f"Description: {task_info['description']}")
        print(f"Status: {'Completed' if task_info['status'] else 'Not Completed'}")
        print(f"Due Date: {task_info['due_date']}")
        print("-" * 20)

```