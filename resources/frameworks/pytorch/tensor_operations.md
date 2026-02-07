#pytorch #tensors #operations

# PyTorch Tensor Operations

## Indexing and Slicing

```python
x = torch.randn(4, 5, 6)

# Basic indexing (similar to NumPy)
x[0]           # First element along dim 0
x[0, 1]        # Element at position (0, 1)
x[0, 1, 2]     # Element at position (0, 1, 2)

# Slicing
x[:, 0]        # All rows, first column
x[0, :]        # First row, all columns
x[1:3]         # Rows 1 and 2
x[:, ::2]      # Every other column
x[..., 0]      # Same as x[:, :, 0]

# Boolean indexing
mask = x > 0
positive_values = x[mask]

# Advanced indexing
indices = torch.tensor([0, 2, 3])
x[indices]     # Select rows 0, 2, 3
```

## Reshaping Operations

```python
x = torch.randn(4, 5, 6)

# Reshape
x.view(20, 6)          # Returns view (shares memory)
x.reshape(20, 6)       # Returns view if possible, copy otherwise
x.view(-1)             # Flatten to 1D
x.view(2, -1)          # Infer one dimension

# Transpose
x.t()                  # 2D transpose only
x.transpose(0, 1)      # Swap dimensions 0 and 1
x.permute(2, 0, 1)     # Reorder dimensions

# Squeeze and unsqueeze
x = torch.randn(1, 4, 1, 6)
x.squeeze()            # Remove all dimensions of size 1 → [4, 6]
x.squeeze(0)           # Remove dimension 0 → [4, 1, 6]
x.unsqueeze(0)         # Add dimension at position 0
x.unsqueeze(-1)        # Add dimension at end

# Flatten
x.flatten()            # Flatten all dimensions
x.flatten(1)           # Flatten from dimension 1 onwards

# Contiguous memory (sometimes needed after transpose/permute)
x = x.contiguous()
```

## Mathematical Operations

### Element-wise Operations

```python
x = torch.randn(3, 4)
y = torch.randn(3, 4)

# Basic arithmetic
z = x + y              # Addition
z = x - y              # Subtraction
z = x * y              # Element-wise multiplication
z = x / y              # Element-wise division
z = x ** 2             # Power

# In-place operations (suffix with _)
x.add_(y)              # x = x + y (in-place)
x.mul_(2)              # x = x * 2 (in-place)

# Math functions
torch.abs(x)           # Absolute value
torch.sqrt(x)          # Square root
torch.exp(x)           # Exponential
torch.log(x)           # Natural logarithm
torch.sin(x)           # Sine
torch.cos(x)           # Cosine
torch.tanh(x)          # Hyperbolic tangent
torch.sigmoid(x)       # Sigmoid: 1/(1+e^-x)
```

### Reduction Operations

```python
x = torch.randn(3, 4, 5)

# Sum
x.sum()                # Sum all elements
x.sum(dim=0)           # Sum along dimension 0
x.sum(dim=[0, 2])      # Sum along dimensions 0 and 2
x.sum(dim=1, keepdim=True)  # Keep dimension

# Mean
x.mean()               # Mean of all elements
x.mean(dim=1)          # Mean along dimension 1

# Other reductions
x.max()                # Maximum value
x.max(dim=1)           # Max along dim 1 (returns values and indices)
x.min()                # Minimum value
x.argmax()             # Index of maximum value
x.argmax(dim=1)        # Indices of max values along dim 1
x.std()                # Standard deviation
x.var()                # Variance
x.prod()               # Product of all elements
```

## Matrix Operations

```python
# Matrix multiplication
a = torch.randn(3, 4)
b = torch.randn(4, 5)
c = torch.matmul(a, b)     # [3, 5]
c = a @ b                   # Same using @ operator

# Batch matrix multiplication
a = torch.randn(10, 3, 4)  # Batch of 10 matrices
b = torch.randn(10, 4, 5)
c = torch.bmm(a, b)        # [10, 3, 5]

# Matrix-vector multiplication
a = torch.randn(3, 4)
v = torch.randn(4)
result = torch.mv(a, v)    # [3]

# Outer product
v1 = torch.randn(3)
v2 = torch.randn(4)
result = torch.outer(v1, v2)  # [3, 4]

# Dot product
v1 = torch.randn(5)
v2 = torch.randn(5)
result = torch.dot(v1, v2)  # scalar
```

## Broadcasting

PyTorch automatically broadcasts tensors of different shapes during operations:

```python
# Same rules as NumPy
a = torch.randn(3, 1, 4)
b = torch.randn(1, 5, 4)
c = a + b                   # Result: [3, 5, 4]

# Common broadcasting patterns
x = torch.randn(10, 5)
bias = torch.randn(5)
result = x + bias           # bias broadcasted to each row

# Explicit broadcasting
a = torch.randn(3, 1)
b = torch.randn(1, 4)
c = a.expand(3, 4) + b.expand(3, 4)
```

## Concatenation and Stacking

```python
x = torch.randn(2, 3)
y = torch.randn(2, 3)
z = torch.randn(2, 3)

# Concatenate along existing dimension
torch.cat([x, y, z], dim=0)      # [6, 3]
torch.cat([x, y, z], dim=1)      # [2, 9]

# Stack creates new dimension
torch.stack([x, y, z], dim=0)    # [3, 2, 3]
torch.stack([x, y, z], dim=1)    # [2, 3, 3]

# Split
x = torch.randn(10, 5)
chunks = torch.chunk(x, 2, dim=0)     # Split into 2 equal parts
splits = torch.split(x, [3, 3, 4], dim=0)  # Split into specific sizes
```

## Comparison Operations

```python
x = torch.randn(3, 4)
y = torch.randn(3, 4)

# Element-wise comparison (returns bool tensor)
x > y
x >= y
x < y
x <= y
x == y
x != y

# Logical operations
torch.logical_and(x > 0, x < 1)
torch.logical_or(x < 0, x > 1)
torch.logical_not(x > 0)

# Any/All
mask = x > 0
mask.any()             # True if any element is True
mask.all()             # True if all elements are True
```

## Advanced Indexing

```python
# Gather - select elements along dimension
x = torch.randn(3, 4)
indices = torch.tensor([[0, 1], [2, 3], [1, 2]])
result = torch.gather(x, 1, indices)  # [3, 2]

# Scatter - write elements at indices
x = torch.zeros(3, 5)
indices = torch.tensor([[0, 1], [2, 3], [1, 2]])
src = torch.randn(3, 2)
x.scatter_(1, indices, src)

# Index select
x = torch.randn(3, 4)
indices = torch.tensor([0, 2])
result = torch.index_select(x, 0, indices)  # Select rows 0 and 2

# Masked fill
x = torch.randn(3, 4)
mask = x > 0
x.masked_fill_(mask, 0)  # Set all positive values to 0
```

## Special Operations

```python
# Clamp (clip values)
x = torch.randn(3, 4)
x.clamp(min=-1, max=1)      # Clip values to [-1, 1]

# Where (conditional selection)
x = torch.randn(3, 4)
y = torch.randn(3, 4)
torch.where(x > 0, x, y)    # Select from x where condition is true, else from y

# Unique
x = torch.tensor([1, 2, 2, 3, 3, 3])
torch.unique(x)             # tensor([1, 2, 3])

# Topk (top k elements)
x = torch.randn(10)
values, indices = torch.topk(x, 3)  # Top 3 values and their indices

# Sort
x = torch.randn(10)
values, indices = torch.sort(x)     # Sorted values and original indices
```

## Einstein Summation (einsum)

Powerful notation for tensor operations:

```python
# Matrix multiplication
a = torch.randn(3, 4)
b = torch.randn(4, 5)
c = torch.einsum('ik,kj->ij', a, b)  # [3, 5]

# Batch matrix multiplication
a = torch.randn(10, 3, 4)
b = torch.randn(10, 4, 5)
c = torch.einsum('bik,bkj->bij', a, b)  # [10, 3, 5]

# Trace
a = torch.randn(5, 5)
trace = torch.einsum('ii->', a)

# Batch trace
a = torch.randn(10, 5, 5)
traces = torch.einsum('bii->b', a)  # [10]

# Transpose
a = torch.randn(3, 4)
b = torch.einsum('ij->ji', a)  # [4, 3]

# Sum along axis
a = torch.randn(3, 4, 5)
b = torch.einsum('ijk->ik', a)  # [3, 5]
```

## Performance Tips

- Use in-place operations (operations ending with `_`) to save memory when possible
- Use `torch.no_grad()` during inference to save memory and computation
- Prefer `view()` over `reshape()` when you know the tensor is contiguous
- Use broadcasting instead of explicit expansion
- Profile your code with `torch.autograd.profiler` to identify bottlenecks

## Related Notes

- [[Main Notes]] - PyTorch fundamentals
- [[Neural Network Modules]] - Using tensors in neural networks
- [[deep_learning]] - Deep learning concepts
