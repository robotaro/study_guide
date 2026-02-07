#pytorch #deep-learning #framework

# PyTorch Core Concepts

## Installation

```bash
# CPU only
pip install torch torchvision torchaudio

# CUDA 11.8
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118

# CUDA 12.1
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
```

## Basic Imports

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
import torch.optim as optim
from torch.utils.data import Dataset, DataLoader
import torchvision
import torchvision.transforms as transforms
```

## Tensors - The Foundation

Tensors are the fundamental data structure in PyTorch, similar to NumPy arrays but with GPU acceleration support.

### Creating Tensors

```python
# From Python lists
x = torch.tensor([[1, 2], [3, 4]])

# Uninitialized tensor (faster, but contains garbage values)
x = torch.empty(3, 4)

# Zero tensor
x = torch.zeros(3, 4)

# Ones tensor
x = torch.ones(3, 4)

# Random tensor (uniform [0, 1))
x = torch.rand(3, 4)

# Random tensor (normal distribution, mean=0, std=1)
x = torch.randn(3, 4)

# From numpy array
import numpy as np
np_array = np.array([[1, 2], [3, 4]])
x = torch.from_numpy(np_array)

# Like another tensor (same shape, different data)
x = torch.zeros_like(other_tensor)
x = torch.randn_like(other_tensor)
```

### Tensor Attributes

```python
x = torch.randn(3, 4, 5)

print(x.shape)      # torch.Size([3, 4, 5])
print(x.size())     # Same as shape
print(x.dtype)      # torch.float32
print(x.device)     # cpu or cuda:0
print(x.requires_grad)  # False by default
```

### Data Types

```python
# Common dtypes
torch.float32 or torch.float    # 32-bit floating point (default)
torch.float64 or torch.double   # 64-bit floating point
torch.float16 or torch.half     # 16-bit floating point
torch.int32 or torch.int        # 32-bit integer
torch.int64 or torch.long       # 64-bit integer (default for integers)
torch.bool                       # Boolean

# Converting dtypes
x = torch.randn(3, 4)
x = x.to(torch.float16)
x = x.half()  # Shortcut for float16
x = x.double()  # Shortcut for float64
```

## Device Management (CPU/GPU)

```python
# Check if CUDA is available
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
print(f"Using device: {device}")

# Create tensor on specific device
x = torch.randn(3, 4, device='cuda')

# Move tensor to device
x = torch.randn(3, 4)
x = x.to(device)
x = x.cuda()  # Move to GPU (if available)
x = x.cpu()   # Move to CPU

# Multiple GPUs
if torch.cuda.device_count() > 1:
    print(f"Using {torch.cuda.device_count()} GPUs")
    model = nn.DataParallel(model)
```

## Autograd - Automatic Differentiation

PyTorch's autograd system automatically computes gradients for backpropagation.

```python
# Enable gradient tracking
x = torch.randn(3, 4, requires_grad=True)

# Or enable later
x = torch.randn(3, 4)
x.requires_grad_(True)  # In-place operation

# Forward pass
y = x ** 2
z = y.mean()

# Backward pass (compute gradients)
z.backward()

# Access gradients
print(x.grad)  # dz/dx

# Prevent gradient tracking (for inference)
with torch.no_grad():
    y = x ** 2  # No gradient computation

# Detach from computation graph
y = x.detach()  # y shares data with x but doesn't track gradients
```

### Important Autograd Concepts

- `requires_grad=True` tells PyTorch to track operations for automatic differentiation
- `.backward()` computes gradients with respect to all tensors with `requires_grad=True`
- Gradients accumulate by default - use `optimizer.zero_grad()` or `tensor.grad.zero_()` to clear
- Use `torch.no_grad()` context for inference to save memory

## Common Operations

See [[Tensor Operations]] for comprehensive tensor manipulation guide.

## Neural Networks

See [[Neural Network Modules]] for detailed nn.Module guide.

## Training Loop

See [[Training Loop]] for training patterns and best practices.

## Data Loading

See [[Data Loading]] for Dataset and DataLoader usage.

## Saving and Loading Models

```python
# Save entire model (architecture + weights)
torch.save(model, 'model.pth')
model = torch.load('model.pth')

# Save only state dict (recommended)
torch.save(model.state_dict(), 'model_weights.pth')
model = MyModel()
model.load_state_dict(torch.load('model_weights.pth'))

# Save checkpoint (for resuming training)
torch.save({
    'epoch': epoch,
    'model_state_dict': model.state_dict(),
    'optimizer_state_dict': optimizer.state_dict(),
    'loss': loss,
}, 'checkpoint.pth')

# Load checkpoint
checkpoint = torch.load('checkpoint.pth')
model.load_state_dict(checkpoint['model_state_dict'])
optimizer.load_state_dict(checkpoint['optimizer_state_dict'])
epoch = checkpoint['epoch']
loss = checkpoint['loss']
```

## Common Patterns

### Setting Random Seeds (Reproducibility)

```python
import random
import numpy as np

def set_seed(seed=42):
    random.seed(seed)
    np.random.seed(seed)
    torch.manual_seed(seed)
    torch.cuda.manual_seed(seed)
    torch.cuda.manual_seed_all(seed)  # for multi-GPU
    torch.backends.cudnn.deterministic = True
    torch.backends.cudnn.benchmark = False
```

### Freezing Layers

```python
# Freeze all parameters
for param in model.parameters():
    param.requires_grad = False

# Freeze specific layers
for param in model.encoder.parameters():
    param.requires_grad = False

# Unfreeze
for param in model.parameters():
    param.requires_grad = True
```

## Related Notes

- [[Tensor Operations]] - Comprehensive tensor manipulation
- [[Neural Network Modules]] - Building neural networks
- [[Training Loop]] - Training patterns
- [[Data Loading]] - Dataset and DataLoader
- [[deep_learning]] - General deep learning concepts
