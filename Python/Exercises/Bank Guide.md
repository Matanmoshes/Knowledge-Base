
### Step 1: Define the `Transaction` Class

1. **Attributes:**
   - `type`: A string indicating the type of transaction ('deposit' or 'withdrawal').
   - `amount`: A float representing the amount of the transaction.

2. **Initialization:**
   - Define an `__init__` method to initialize these attributes.

### Step 2: Define the `BankAccount` Class

1. **Attributes:**
   - `owner_name`: A string representing the name of the account owner.
   - `id`: A string representing the account ID.
   - `state`: A string representing the state of the account (e.g., 'active', 'closed').
   - `transactions`: A list to store the `Transaction` objects.

2. **Initialization:**
   - Define an `__init__` method to initialize these attributes, including an empty list for transactions.

### Step 3: Add Methods to the `BankAccount` Class

1. **Method to Add Transactions:**
   - Define a method `add_transaction` that takes `type` and `amount` as parameters.
   - Create a `Transaction` object with the given `type` and `amount` and add it to the `transactions` list.

2. **Method to Get Deposits:**
   - Define a method `get_deposits` that returns a list of all deposits (filter transactions where `type` is 'deposit').

3. **Method to Get Withdrawals:**
   - Define a method `get_withdrawals` that returns a list of all withdrawals (filter transactions where `type` is 'withdrawal').

### Step 4: Instantiate the `BankAccount` Class and Add Transactions

1. **Create an Instance:**
   - Instantiate the `BankAccount` class with an owner's name, an account ID, and an initial state.

2. **Add Transactions:**
   - Use the `add_transaction` method to add several transactions (both deposits and withdrawals) to the account.

### Step 5: Display the Results

1. **Get and Print Deposits:**
   - Call the `get_deposits` method and print the result.

2. **Get and Print Withdrawals:**
   - Call the `get_withdrawals` method and print the result.

### Example Flow

1. **Create the `Transaction` class.**
2. **Create the `BankAccount` class with the necessary attributes and methods.**
3. **Instantiate a `BankAccount` object.**
4. **Add transactions using the `add_transaction` method.**
5. **Print separate lists of deposits and withdrawals.**

### Tips

- Use list comprehensions for filtering transactions in `get_deposits` and `get_withdrawals` methods.
- Ensure the `type` attribute in `Transaction` class is validated (only 'deposit' or 'withdrawal' allowed).



# <span style="color:#ff0000">Dont look this is only for reference</span>
----
### Sample Pseudocode

1. **Transaction Class:**
   ```python
   class Transaction:
       def __init__(self, type, amount):
           self.type = type
           self.amount = amount
   ```

2. **BankAccount Class:**
   ```python
   class BankAccount:
       def __init__(self, owner_name, id, state):
           self.owner_name = owner_name
           self.id = id
           self.state = state
           self.transactions = []

       def add_transaction(self, type, amount):
           self.transactions.append(Transaction(type, amount))

       def get_deposits(self):
           return [t for t in self.transactions if t.type == 'deposit']

       def get_withdrawals(self):
           return [t for t in self.transactions if t.type == 'withdrawal']
   ```

3. **Instantiate and Use:**
   ```python
   account = BankAccount('John Doe', '12345', 'active')
   account.add_transaction('deposit', 100.0)
   account.add_transaction('withdrawal', 50.0)
   account.add_transaction('deposit', 200.0)

   deposits = account.get_deposits()
   withdrawals = account.get_withdrawals()

   print(deposits)
   print(withdrawals)
   ```

This step-by-step guide should help you structure and implement the desired functionality in Python.