# Demo 5

## Jupyter Notebook

This part of the demo is a jupyter notebook that demonstrates how to train a random forest classifier on a large synthetic dataset while monitoring the CPU usage.

### First cell - imports
```python
import numpy as np
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
import matplotlib.pyplot as plt
import psutil
import time
from IPython.display import clear_output
import threading
import gc

def plot_cpu_usage(cpu_history, time_history):
    plt.figure(figsize=(10, 6))
    plt.plot(time_history, cpu_history)
    plt.xlabel('Time (seconds)')
    plt.ylabel('CPU Usage (%)')
    plt.title('CPU Usage Over Time')
    plt.grid(True)
    plt.show()
```

### Second cell - Generate a large synthetic dataset
```python
n_samples = 100000
n_features = 100

print("Generating large dataset...")
X = np.random.randn(n_samples, n_features)
y = np.random.randint(0, 2, n_samples)  # Binary classification

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
print(f"Training data shape: {X_train.shape}")

# Make the training data larger
X_train = np.repeat(X_train, 3, axis=0)
y_train = np.repeat(y_train, 3, axis=0)
```

### Third cell - Training with CPU monitoring
```python
# Make the model more computationally intensive
rf_model = RandomForestClassifier(
    n_estimators=500,  # Increased from 100
    max_depth=None,
    min_samples_split=2,
    n_jobs=-1
)

# Clear any cached data
gc.collect()

cpu_history = []
time_history = []
start_time = time.time()

print("Starting model training...")
monitor_thread = threading.Thread(target=rf_model.fit, args=(X_train, y_train))
monitor_thread.start()

# Remove the length limit and only stop when training is done
while monitor_thread.is_alive():  # Removed len(time_history) < 100 condition
    current_time = time.time() - start_time
    cpu_percent = psutil.cpu_percent(interval=0.1)
    
    cpu_history.append(cpu_percent)
    time_history.append(current_time)
    
    clear_output(wait=True)
    plot_cpu_usage(cpu_history, time_history)
    time.sleep(0.2)

monitor_thread.join()
print("Training completed!")
print(f"Total training time: {time_history[-1]:.2f} seconds")
```

### Running the notebook

If running from cursor or vscode, you need to activate the virtual environment.

```bash
source venv/bin/activate
```

