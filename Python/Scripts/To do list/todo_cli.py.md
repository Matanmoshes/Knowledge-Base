
```python
# todo_cli.py

from todo_functions import add_task, remove_task, update_task_status, list_tasks

def main():
    while True:
        print("To-Do List Manager")
        print("1. Add Task")
        print("2. Remove Task")
        print("3. Update Task Status")
        print("4. List All Tasks")
        print("5. Exit")
        choice = input("Enter your choice: ")
        
        if choice == "1":
            try:
                task_id = int(input("Enter Task ID: "))
                description = input("Enter Task Description: ")
			    status = input("Is the task completed? (yes/no):     ").strip().lower() == "yes"
                due_date = input("Enter Due Date (YYYY-MM-DD): ")
                add_task(task_id, description, status, due_date)
                print("Task added successfully.")
            except ValueError as e:
                print(e)
        elif choice == "2":
            try:
                task_id = int(input("Enter Task ID to remove: "))
                remove_task(task_id)
                print("Task removed successfully.")
            except ValueError as e:
                print(e)
        elif choice == "3":
            try:
                task_id = int(input("Enter Task ID to update: "))
                status = input("Is the task completed? (yes/no): ").strip().lower() == "yes"
                update_task_status(task_id, status)
                print("Task status updated successfully.")
            except ValueError as e:
                print(e)
        elif choice == "4":
            list_tasks()
        elif choice == "5":
            break
        else:
            print("Invalid choice. Please try again.")

if __name__ == "__main__":
    main()

```