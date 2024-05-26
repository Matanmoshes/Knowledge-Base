

```python
import random
import matplotlib.pyplot as plt

# Function to simulate a single coin flip
def coin_flip():
    return "heads" if random.choice([True, False]) else "tails"

# Function to simulate multiple coin flips
def multiple_coin_flips(n):
    results = []
    for _ in range(n):
        results.append(coin_flip())
    return results

# Function to track the results and write them to a file
def track_results(results, filename):
    heads_count = results.count("heads")
    tails_count = results.count("tails")

    with open(filename, "w") as file:
        file.write(f"heads: {heads_count}\n")
        file.write(f"tails: {tails_count}\n")
    print(f"Results written to {filename}")

# Finction to display the results as bar chart
def display_results(filename):
    with open(filename, "r") as file:
        data = file.readlines()
    
    # Parse the results as bar chart
    results = {line.split(": ")[0]: int(line.split(": ")[1]) for line in data}
    labels = list(results.keys())
    counts = list(results.values())

    # Create a bar chart using matplotlib
    plt.bar(labels, counts, color=['blue', 'orange'])
    plt.xlabel('Outcome')
    plt.ylabel('Count')
    plt.title('Coin Flip Results')
    plt.show()

# Usage
if __name__ == "__main__":
    filename = "coin_flip_results.txt" 
    n = 100 # The number of coin flips
    results = multiple_coin_flips(n)
    track_results(results, filename)
    display_results(filename)

```

---
