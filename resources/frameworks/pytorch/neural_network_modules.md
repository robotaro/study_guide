#pytorch #neural-networks #architecture

# PyTorch Neural Network Modules

## nn.Module - The Base Class

All neural networks in PyTorch should inherit from `nn.Module`:

```python
import torch
import torch.nn as nn
import torch.nn.functional as F

class MyModel(nn.Module):
    def __init__(self):
        super(MyModel, self).__init__()  # Always call parent constructor
        # Define layers here
        self.fc1 = nn.Linear(784, 256)
        self.fc2 = nn.Linear(256, 10)

    def forward(self, x):
        # Define forward pass
        x = F.relu(self.fc1(x))
        x = self.fc2(x)
        return x

# Usage
model = MyModel()
output = model(input_tensor)  # Calls forward() automatically
```

### Key nn.Module Methods

```python
# Get all parameters
for param in model.parameters():
    print(param.shape)

# Get named parameters
for name, param in model.named_parameters():
    print(f"{name}: {param.shape}")

# Move model to device
model.to(device)
model.cuda()  # Move to GPU
model.cpu()   # Move to CPU

# Set training/evaluation mode
model.train()  # Enable dropout, batch norm in training mode
model.eval()   # Disable dropout, batch norm in eval mode

# Get number of parameters
num_params = sum(p.numel() for p in model.parameters())
trainable_params = sum(p.numel() for p in model.parameters() if p.requires_grad)
```

## Common Layers

### Linear Layers (Fully Connected)

```python
# Linear(in_features, out_features, bias=True)
fc = nn.Linear(512, 256)

# Usage
x = torch.randn(32, 512)  # Batch of 32
output = fc(x)            # [32, 256]

# Access weights and biases
print(fc.weight.shape)    # [256, 512]
print(fc.bias.shape)      # [256]
```

### Convolutional Layers

```python
# 2D Convolution
# Conv2d(in_channels, out_channels, kernel_size, stride=1, padding=0)
conv = nn.Conv2d(3, 64, kernel_size=3, stride=1, padding=1)

# Usage
x = torch.randn(32, 3, 224, 224)  # [batch, channels, height, width]
output = conv(x)                   # [32, 64, 224, 224]

# 1D Convolution (for sequences)
conv1d = nn.Conv1d(16, 32, kernel_size=3)
x = torch.randn(32, 16, 100)  # [batch, channels, length]
output = conv1d(x)

# Transposed Convolution (for upsampling)
deconv = nn.ConvTranspose2d(64, 3, kernel_size=4, stride=2, padding=1)
```

### Pooling Layers

```python
# Max Pooling
maxpool = nn.MaxPool2d(kernel_size=2, stride=2)
x = torch.randn(32, 64, 56, 56)
output = maxpool(x)  # [32, 64, 28, 28]

# Average Pooling
avgpool = nn.AvgPool2d(kernel_size=2, stride=2)

# Adaptive Pooling (output size is specified)
adaptive_pool = nn.AdaptiveAvgPool2d((1, 1))  # Output: [batch, channels, 1, 1]
x = torch.randn(32, 512, 7, 7)
output = adaptive_pool(x)  # [32, 512, 1, 1]

# Global Average Pooling pattern
x = x.mean(dim=[2, 3])  # Average over spatial dimensions
```

### Normalization Layers

```python
# Batch Normalization (normalizes across batch dimension)
bn = nn.BatchNorm2d(64)  # For 2D inputs (images)
bn1d = nn.BatchNorm1d(128)  # For 1D inputs (sequences, vectors)

# Layer Normalization (normalizes across features)
ln = nn.LayerNorm(512)

# Instance Normalization (normalizes per instance)
in_norm = nn.InstanceNorm2d(64)

# Group Normalization
gn = nn.GroupNorm(32, 64)  # 32 groups, 64 channels

# Usage example
x = torch.randn(32, 64, 56, 56)
x = bn(x)  # Applies batch normalization
```

### Dropout

```python
# Standard dropout
dropout = nn.Dropout(p=0.5)  # Drop 50% of neurons

# 2D dropout (for images, drops entire channels)
dropout2d = nn.Dropout2d(p=0.5)

# Usage - only active in training mode
model.train()
x = dropout(x)  # Applies dropout

model.eval()
x = dropout(x)  # Does nothing in eval mode
```

### Recurrent Layers

```python
# LSTM
lstm = nn.LSTM(input_size=100, hidden_size=256, num_layers=2, batch_first=True)
x = torch.randn(32, 10, 100)  # [batch, sequence_length, input_size]
output, (h_n, c_n) = lstm(x)
# output: [32, 10, 256] - outputs for each timestep
# h_n: [2, 32, 256] - final hidden states for each layer
# c_n: [2, 32, 256] - final cell states for each layer

# GRU
gru = nn.GRU(input_size=100, hidden_size=256, num_layers=2, batch_first=True)
output, h_n = gru(x)

# Bidirectional RNN
bi_lstm = nn.LSTM(100, 256, num_layers=2, bidirectional=True, batch_first=True)
output, (h_n, c_n) = bi_lstm(x)
# output: [32, 10, 512] - 256*2 for bidirectional
```

### Attention and Transformer Layers

```python
# Multi-head Attention
attention = nn.MultiheadAttention(embed_dim=512, num_heads=8)
query = torch.randn(10, 32, 512)  # [seq_len, batch, embed_dim]
key = torch.randn(10, 32, 512)
value = torch.randn(10, 32, 512)
output, weights = attention(query, key, value)

# Transformer Encoder Layer
encoder_layer = nn.TransformerEncoderLayer(d_model=512, nhead=8)
transformer_encoder = nn.TransformerEncoder(encoder_layer, num_layers=6)
x = torch.randn(10, 32, 512)
output = transformer_encoder(x)

# Full Transformer
transformer = nn.Transformer(d_model=512, nhead=8, num_encoder_layers=6,
                             num_decoder_layers=6)
```

### Embedding Layers

```python
# Word embeddings
embedding = nn.Embedding(num_embeddings=10000, embedding_dim=300)
# num_embeddings: vocabulary size
# embedding_dim: size of embedding vectors

# Usage
indices = torch.tensor([1, 5, 3, 2])  # Word indices
embedded = embedding(indices)  # [4, 300]

# Pre-trained embeddings
embedding = nn.Embedding(10000, 300)
embedding.weight.data.copy_(pretrained_vectors)
embedding.weight.requires_grad = False  # Freeze embeddings
```

## Activation Functions

```python
# As layers (stateful)
relu = nn.ReLU()
sigmoid = nn.Sigmoid()
tanh = nn.Tanh()
leaky_relu = nn.LeakyReLU(negative_slope=0.01)
elu = nn.ELU()
gelu = nn.GELU()  # Popular in transformers
silu = nn.SiLU()  # Also called Swish

# As functions (functional API - no state)
import torch.nn.functional as F
x = F.relu(x)
x = F.sigmoid(x)
x = F.tanh(x)
x = F.leaky_relu(x, negative_slope=0.01)
x = F.gelu(x)

# Softmax
softmax = nn.Softmax(dim=1)  # Apply along dimension 1
x = F.softmax(x, dim=1)  # Functional version
```

## Loss Functions

```python
# Classification losses
criterion = nn.CrossEntropyLoss()  # Combines log_softmax and NLLLoss
# Input: [batch_size, num_classes], Target: [batch_size] (class indices)
loss = criterion(predictions, targets)

criterion = nn.NLLLoss()  # Negative Log Likelihood
criterion = nn.BCELoss()  # Binary Cross Entropy (requires sigmoid output)
criterion = nn.BCEWithLogitsLoss()  # BCE with sigmoid (more stable)

# Regression losses
criterion = nn.MSELoss()  # Mean Squared Error
criterion = nn.L1Loss()   # Mean Absolute Error
criterion = nn.SmoothL1Loss()  # Huber loss
criterion = nn.HuberLoss()

# Other losses
criterion = nn.KLDivLoss()  # KL Divergence
criterion = nn.CTCLoss()    # For sequence-to-sequence (e.g., speech recognition)
criterion = nn.TripletMarginLoss()  # For metric learning
```

## Sequential and ModuleList/Dict

### Sequential

```python
# Simple sequential model
model = nn.Sequential(
    nn.Linear(784, 256),
    nn.ReLU(),
    nn.Dropout(0.5),
    nn.Linear(256, 128),
    nn.ReLU(),
    nn.Linear(128, 10)
)

# Access layers
print(model[0])  # First layer
for layer in model:
    print(layer)
```

### ModuleList

```python
# For dynamic number of layers
class MyModel(nn.Module):
    def __init__(self, num_layers):
        super().__init__()
        self.layers = nn.ModuleList([
            nn.Linear(256, 256) for _ in range(num_layers)
        ])

    def forward(self, x):
        for layer in self.layers:
            x = F.relu(layer(x))
        return x
```

### ModuleDict

```python
# For named, dynamic layers
class MyModel(nn.Module):
    def __init__(self):
        super().__init__()
        self.layers = nn.ModuleDict({
            'encoder': nn.Linear(784, 256),
            'decoder': nn.Linear(256, 784),
            'classifier': nn.Linear(256, 10)
        })

    def forward(self, x, mode='classify'):
        x = self.layers['encoder'](x)
        if mode == 'classify':
            return self.layers['classifier'](x)
        else:
            return self.layers['decoder'](x)
```

## Common Architecture Patterns

### ResNet Block

```python
class ResidualBlock(nn.Module):
    def __init__(self, in_channels, out_channels, stride=1):
        super().__init__()
        self.conv1 = nn.Conv2d(in_channels, out_channels, 3, stride, 1)
        self.bn1 = nn.BatchNorm2d(out_channels)
        self.conv2 = nn.Conv2d(out_channels, out_channels, 3, 1, 1)
        self.bn2 = nn.BatchNorm2d(out_channels)

        # Shortcut connection
        self.shortcut = nn.Sequential()
        if stride != 1 or in_channels != out_channels:
            self.shortcut = nn.Sequential(
                nn.Conv2d(in_channels, out_channels, 1, stride),
                nn.BatchNorm2d(out_channels)
            )

    def forward(self, x):
        identity = x
        out = F.relu(self.bn1(self.conv1(x)))
        out = self.bn2(self.conv2(out))
        out += self.shortcut(identity)
        out = F.relu(out)
        return out
```

### Encoder-Decoder

```python
class Encoder(nn.Module):
    def __init__(self):
        super().__init__()
        self.layers = nn.Sequential(
            nn.Conv2d(3, 64, 3, 1, 1),
            nn.ReLU(),
            nn.MaxPool2d(2),
            nn.Conv2d(64, 128, 3, 1, 1),
            nn.ReLU(),
            nn.MaxPool2d(2)
        )

    def forward(self, x):
        return self.layers(x)

class Decoder(nn.Module):
    def __init__(self):
        super().__init__()
        self.layers = nn.Sequential(
            nn.ConvTranspose2d(128, 64, 4, 2, 1),
            nn.ReLU(),
            nn.ConvTranspose2d(64, 3, 4, 2, 1),
            nn.Sigmoid()
        )

    def forward(self, x):
        return self.layers(x)
```

## Weight Initialization

```python
def init_weights(m):
    if isinstance(m, nn.Linear):
        nn.init.xavier_uniform_(m.weight)
        if m.bias is not None:
            nn.init.zeros_(m.bias)
    elif isinstance(m, nn.Conv2d):
        nn.init.kaiming_normal_(m.weight, mode='fan_out', nonlinearity='relu')
    elif isinstance(m, nn.BatchNorm2d):
        nn.init.constant_(m.weight, 1)
        nn.init.constant_(m.bias, 0)

# Apply to model
model = MyModel()
model.apply(init_weights)
```

## Related Notes

- [[Main Notes]] - PyTorch fundamentals
- [[Tensor Operations]] - Tensor manipulation
- [[Training Loop]] - How to train models
- [[Attention Mechanism]] - Attention in detail
- [[deep_learning]] - Deep learning concepts
