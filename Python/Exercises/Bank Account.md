
```python
class Transaction:
    def __init__(self, transaction_type, amount):
        if transaction_type not in ['deposit', 'withdrawal']:
            raise ValueError("Transaction type must be 'deposit' or 'withdrawal'")
        self.transaction_type = transaction_type
        self.amount = amount

class BankAccount:
    def __init__(self, owner_name, account_id, state):
        self.owner_name = owner_name
        self.account_id = account_id
        self.state = state
        self.transactions = []

    def add_transaction(self, transaction):
        if not isinstance(transaction, Transaction):
            raise TypeError("Must add an instance of Transaction")
        self.transactions.append(transaction)

    def get_deposits(self):
        return [t.amount for t in self.transactions if t.transaction_type == 'deposit']

    def get_withdrawals(self):
        return [t.amount for t in self.transactions if t.transaction_type == 'withdrawal']

# Demonstration
# Creating an instance of BankAccount
account = BankAccount("John Doe", "12345", "CA")

# Adding transactions
account.add_transaction(Transaction("deposit", 1000))
account.add_transaction(Transaction("withdrawal", 200))
account.add_transaction(Transaction("deposit", 500))
account.add_transaction(Transaction("withdrawal", 300))

# Getting separate lists of deposits and withdrawals
deposits = account.get_deposits()
withdrawals = account.get_withdrawals()

print("Deposits:", deposits)
print("Withdrawals:", withdrawals)

```


---
### Class Definitions

#### Transaction Class

The `Transaction` class represents a single financial transaction. This could be either a deposit or a withdrawal.

1. **Constructor (`__init__` method):**
   ```python
   class Transaction:
       def __init__(self, transaction_type, amount):
           if transaction_type not in ['deposit', 'withdrawal']:
               raise ValueError("Transaction type must be 'deposit' or 'withdrawal'")
           self.transaction_type = transaction_type
           self.amount = amount
   ```
   - **Parameters:** 
     - `transaction_type`: This is a string that indicates the type of transaction. It must be either 'deposit' or 'withdrawal'.
     - `amount`: This is a numeric value that represents the amount of money involved in the transaction.
   - **Logic:**
     - The constructor first checks if `transaction_type` is either 'deposit' or 'withdrawal'. If it's neither, a `ValueError` is raised, ensuring that only valid transaction types are used.
     - If the `transaction_type` is valid, the constructor assigns the values of `transaction_type` and `amount` to the instance variables `self.transaction_type` and `self.amount`.

#### BankAccount Class

The `BankAccount` class represents a bank account that can hold multiple transactions (both deposits and withdrawals).

1. **Constructor (`__init__` method):**
   ```python
   class BankAccount:
       def __init__(self, owner_name, account_id, state):
           self.owner_name = owner_name
           self.account_id = account_id
           self.state = state
           self.transactions = []
   ```
   - **Parameters:**
     - `owner_name`: This is a string that represents the name of the account owner.
     - `account_id`: This is a string that represents the unique identifier for the bank account.
     - `state`: This is a string that could represent the state of the account or the geographical state.
   - **Logic:**
     - The constructor initializes the account with the provided `owner_name`, `account_id`, and `state`.
     - It also initializes an empty list `self.transactions` to store transaction objects.

2. **Method `add_transaction`:**
   ```python
   def add_transaction(self, transaction):
       if not isinstance(transaction, Transaction):
           raise TypeError("Must add an instance of Transaction")
       self.transactions.append(transaction)
   ```
   - **Parameters:**
     - `transaction`: This parameter is expected to be an instance of the `Transaction` class.
   - **Logic:**
     - The method first checks if the provided `transaction` is indeed an instance of the `Transaction` class. If not, it raises a `TypeError`, ensuring that only valid transactions are added to the account.
     - If the `transaction` is valid, it appends the transaction to the `self.transactions` list.

3. **Method `get_deposits`:**
   ```python
   def get_deposits(self):
       return [t.amount for t in self.transactions if t.transaction_type == 'deposit']
   ```
   - **Logic:**
     - This method iterates over the `self.transactions` list and collects the amounts of all transactions that are of type 'deposit'.
     - It returns a list of these deposit amounts.

4. **Method `get_withdrawals`:**
   ```python
   def get_withdrawals(self):
       return [t.amount for t in self.transactions if t.transaction_type == 'withdrawal']
   ```
   - **Logic:**
     - This method iterates over the `self.transactions` list and collects the amounts of all transactions that are of type 'withdrawal'.
     - It returns a list of these withdrawal amounts.

### Demonstration

1. **Creating an instance of `BankAccount`:**
   ```python
   account = BankAccount("John Doe", "12345", "CA")
   ```
   - This creates a `BankAccount` instance with the owner name "John Doe", account ID "12345", and state "CA".
   - The `self.transactions` list is initialized as empty at this point.

2. **Adding transactions to the account:**
   ```python
   account.add_transaction(Transaction("deposit", 1000))
   account.add_transaction(Transaction("withdrawal", 200))
   account.add_transaction(Transaction("deposit", 500))
   account.add_transaction(Transaction("withdrawal", 300))
   ```
   - **First Transaction:** 
     - `Transaction("deposit", 1000)` creates a `Transaction` instance representing a deposit of 1000.
     - `account.add_transaction(...)` adds this transaction to the account's transactions list.
   - **Second Transaction:**
     - `Transaction("withdrawal", 200)` creates a `Transaction` instance representing a withdrawal of 200.
     - `account.add_transaction(...)` adds this transaction to the account's transactions list.
   - **Third Transaction:**
     - `Transaction("deposit", 500)` creates a `Transaction` instance representing a deposit of 500.
     - `account.add_transaction(...)` adds this transaction to the account's transactions list.
   - **Fourth Transaction:**
     - `Transaction("withdrawal", 300)` creates a `Transaction` instance representing a withdrawal of 300.
     - `account.add_transaction(...)` adds this transaction to the account's transactions list.

3. **Getting lists of deposits and withdrawals:**
   ```python
   deposits = account.get_deposits()
   withdrawals = account.get_withdrawals()
   ```
   - **Deposits:**
     - `account.get_deposits()` calls the `get_deposits` method, which iterates over the transactions list and returns a list of deposit amounts `[1000, 500]`.
   - **Withdrawals:**
     - `account.get_withdrawals()` calls the `get_withdrawals` method, which iterates over the transactions list and returns a list of withdrawal amounts `[200, 300]`.

4. **Printing the lists:**
   ```python
   print("Deposits:", deposits)
   print("Withdrawals:", withdrawals)
   ```
   - This prints the lists of deposits and withdrawals:
     - `Deposits: [1000, 500]`
     - `Withdrawals: [200, 300]`

### Summary

The code provides a simple representation of a bank account system where transactions (deposits and withdrawals) can be added and retrieved separately. The `Transaction` class ensures that only valid transaction types are used, and the `BankAccount` class manages these transactions and provides methods to filter and retrieve them by type. The demonstration shows how to create a bank account, add transactions to it, and retrieve lists of deposits and withdrawals.