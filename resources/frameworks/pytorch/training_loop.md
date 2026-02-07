#pytorch #training #optimization

# PyTorch Training Loop

## Basic Training Template

```python
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import DataLoader

# Setup
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
model = MyModel().to(device)
criterion = nn.CrossEntropyLoss()
optimizer = optim.Adam(model.parameters(), lr=0.001)

# Training loop
num_epochs = 10
for epoch in range(num_epochs):
    model.train()  # Set to training mode
    running_loss = 0.0

    for batch_idx, (inputs, targets) in enumerate(train_loader):
        # Move data to device
        inputs = inputs.to(device)
        targets = targets.to(device)

        # Zero gradients
        optimizer.zero_grad()

        # Forward pass
        outputs = model(inputs)
        loss = criterion(outputs, targets)

        # Backward pass
        loss.backward()

        # Update weights
        optimizer.step()

        running_loss += loss.item()

    # Print statistics
    avg_loss = running_loss / len(train_loader)
    print(f'Epoch [{epoch+1}/{num_epochs}], Loss: {avg_loss:.4f}')
```

## Training with Validation

```python
def train_one_epoch(model, train_loader, criterion, optimizer, device):
    model.train()
    running_loss = 0.0
    correct = 0
    total = 0

    for inputs, targets in train_loader:
        inputs, targets = inputs.to(device), targets.to(device)

        optimizer.zero_grad()
        outputs = model(inputs)
        loss = criterion(outputs, targets)
        loss.backward()
        optimizer.step()

        running_loss += loss.item()
        _, predicted = outputs.max(1)
        total += targets.size(0)
        correct += predicted.eq(targets).sum().item()

    train_loss = running_loss / len(train_loader)
    train_acc = 100. * correct / total
    return train_loss, train_acc

def validate(model, val_loader, criterion, device):
    model.eval()
    running_loss = 0.0
    correct = 0
    total = 0

    with torch.no_grad():
        for inputs, targets in val_loader:
            inputs, targets = inputs.to(device), targets.to(device)

            outputs = model(inputs)
            loss = criterion(outputs, targets)

            running_loss += loss.item()
            _, predicted = outputs.max(1)
            total += targets.size(0)
            correct += predicted.eq(targets).sum().item()

    val_loss = running_loss / len(val_loader)
    val_acc = 100. * correct / total
    return val_loss, val_acc

# Full training loop
best_val_acc = 0.0
for epoch in range(num_epochs):
    train_loss, train_acc = train_one_epoch(model, train_loader, criterion,
                                             optimizer, device)
    val_loss, val_acc = validate(model, val_loader, criterion, device)

    print(f'Epoch {epoch+1}/{num_epochs}:')
    print(f'  Train Loss: {train_loss:.4f}, Train Acc: {train_acc:.2f}%')
    print(f'  Val Loss: {val_loss:.4f}, Val Acc: {val_acc:.2f}%')

    # Save best model
    if val_acc > best_val_acc:
        best_val_acc = val_acc
        torch.save(model.state_dict(), 'best_model.pth')
```

## Optimizers

### Common Optimizers

```python
# SGD (Stochastic Gradient Descent)
optimizer = optim.SGD(model.parameters(), lr=0.01, momentum=0.9,
                      weight_decay=1e-4, nesterov=True)

# Adam (Adaptive Moment Estimation)
optimizer = optim.Adam(model.parameters(), lr=0.001, betas=(0.9, 0.999),
                       eps=1e-8, weight_decay=0)

# AdamW (Adam with decoupled weight decay)
optimizer = optim.AdamW(model.parameters(), lr=0.001, weight_decay=0.01)

# RMSprop
optimizer = optim.RMSprop(model.parameters(), lr=0.01, alpha=0.99)

# Adagrad
optimizer = optim.Adagrad(model.parameters(), lr=0.01)

# Different learning rates for different layers
optimizer = optim.Adam([
    {'params': model.encoder.parameters(), 'lr': 1e-4},
    {'params': model.decoder.parameters(), 'lr': 1e-3}
])
```

### Optimizer Operations

```python
# Zero gradients (must do before backward!)
optimizer.zero_grad()

# Alternative: set_to_none is more efficient
optimizer.zero_grad(set_to_none=True)

# Update parameters
optimizer.step()

# Access current learning rate
for param_group in optimizer.param_groups:
    print(param_group['lr'])

# Manually set learning rate
for param_group in optimizer.param_groups:
    param_group['lr'] = 0.0001
```

## Learning Rate Schedulers

```python
from torch.optim.lr_scheduler import (
    StepLR, MultiStepLR, ExponentialLR, ReduceLROnPlateau,
    CosineAnnealingLR, OneCycleLR
)

# Step decay: multiply LR by gamma every step_size epochs
scheduler = StepLR(optimizer, step_size=30, gamma=0.1)

# Multi-step decay: at specific milestones
scheduler = MultiStepLR(optimizer, milestones=[30, 60, 90], gamma=0.1)

# Exponential decay
scheduler = ExponentialLR(optimizer, gamma=0.95)

# Reduce on plateau: reduce when metric stops improving
scheduler = ReduceLROnPlateau(optimizer, mode='min', factor=0.1,
                              patience=10, verbose=True)

# Cosine annealing
scheduler = CosineAnnealingLR(optimizer, T_max=100, eta_min=0)

# One Cycle (popular for fast training)
scheduler = OneCycleLR(optimizer, max_lr=0.01, steps_per_epoch=len(train_loader),
                       epochs=num_epochs)

# Usage in training loop
for epoch in range(num_epochs):
    train_one_epoch(...)

    # For most schedulers: step once per epoch
    scheduler.step()

    # For ReduceLROnPlateau: pass validation metric
    val_loss = validate(...)
    scheduler.step(val_loss)

    # For OneCycleLR: step after each batch (inside train_one_epoch)
```

## Gradient Clipping

Prevent exploding gradients:

```python
# Clip by norm
max_norm = 1.0
torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm)

# Clip by value
torch.nn.utils.clip_grad_value_(model.parameters(), clip_value=0.5)

# Usage in training loop
for inputs, targets in train_loader:
    optimizer.zero_grad()
    outputs = model(inputs)
    loss = criterion(outputs, targets)
    loss.backward()

    # Clip gradients before optimizer step
    torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)

    optimizer.step()
```

## Mixed Precision Training

Faster training with reduced memory using automatic mixed precision:

```python
from torch.cuda.amp import autocast, GradScaler

scaler = GradScaler()

for epoch in range(num_epochs):
    for inputs, targets in train_loader:
        inputs, targets = inputs.to(device), targets.to(device)

        optimizer.zero_grad()

        # Forward pass with autocast
        with autocast():
            outputs = model(inputs)
            loss = criterion(outputs, targets)

        # Backward pass with scaled gradients
        scaler.scale(loss).backward()

        # Unscale gradients and clip
        scaler.unscale_(optimizer)
        torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)

        # Update weights
        scaler.step(optimizer)
        scaler.update()
```

## Early Stopping

```python
class EarlyStopping:
    def __init__(self, patience=7, min_delta=0, verbose=False):
        self.patience = patience
        self.min_delta = min_delta
        self.verbose = verbose
        self.counter = 0
        self.best_loss = None
        self.early_stop = False

    def __call__(self, val_loss):
        if self.best_loss is None:
            self.best_loss = val_loss
        elif val_loss > self.best_loss - self.min_delta:
            self.counter += 1
            if self.verbose:
                print(f'EarlyStopping counter: {self.counter}/{self.patience}')
            if self.counter >= self.patience:
                self.early_stop = True
        else:
            self.best_loss = val_loss
            self.counter = 0

# Usage
early_stopping = EarlyStopping(patience=10, verbose=True)

for epoch in range(num_epochs):
    train_loss = train_one_epoch(...)
    val_loss = validate(...)

    early_stopping(val_loss)
    if early_stopping.early_stop:
        print("Early stopping triggered")
        break
```

## Progress Tracking with tqdm

```python
from tqdm import tqdm

for epoch in range(num_epochs):
    model.train()
    pbar = tqdm(train_loader, desc=f'Epoch {epoch+1}/{num_epochs}')

    for inputs, targets in pbar:
        inputs, targets = inputs.to(device), targets.to(device)

        optimizer.zero_grad()
        outputs = model(inputs)
        loss = criterion(outputs, targets)
        loss.backward()
        optimizer.step()

        # Update progress bar
        pbar.set_postfix({'loss': loss.item()})
```

## Checkpointing

```python
def save_checkpoint(state, filename='checkpoint.pth'):
    torch.save(state, filename)

def load_checkpoint(model, optimizer, filename='checkpoint.pth'):
    checkpoint = torch.load(filename)
    model.load_state_dict(checkpoint['model_state_dict'])
    optimizer.load_state_dict(checkpoint['optimizer_state_dict'])
    return checkpoint['epoch'], checkpoint['loss']

# Save checkpoint
save_checkpoint({
    'epoch': epoch,
    'model_state_dict': model.state_dict(),
    'optimizer_state_dict': optimizer.state_dict(),
    'scheduler_state_dict': scheduler.state_dict(),
    'loss': train_loss,
    'val_acc': val_acc
}, filename='checkpoint.pth')

# Load checkpoint
start_epoch, loss = load_checkpoint(model, optimizer, 'checkpoint.pth')
```

## Logging with TensorBoard

```python
from torch.utils.tensorboard import SummaryWriter

writer = SummaryWriter('runs/experiment_1')

for epoch in range(num_epochs):
    train_loss, train_acc = train_one_epoch(...)
    val_loss, val_acc = validate(...)

    # Log scalars
    writer.add_scalar('Loss/train', train_loss, epoch)
    writer.add_scalar('Loss/val', val_loss, epoch)
    writer.add_scalar('Accuracy/train', train_acc, epoch)
    writer.add_scalar('Accuracy/val', val_acc, epoch)
    writer.add_scalar('Learning Rate', optimizer.param_groups[0]['lr'], epoch)

    # Log histograms
    for name, param in model.named_parameters():
        writer.add_histogram(name, param, epoch)
        if param.grad is not None:
            writer.add_histogram(f'{name}.grad', param.grad, epoch)

writer.close()

# View with: tensorboard --logdir=runs
```

## Metrics Calculation

```python
from sklearn.metrics import accuracy_score, precision_recall_fscore_support, confusion_matrix

def calculate_metrics(model, data_loader, device):
    model.eval()
    all_preds = []
    all_targets = []

    with torch.no_grad():
        for inputs, targets in data_loader:
            inputs = inputs.to(device)
            outputs = model(inputs)
            _, predicted = outputs.max(1)

            all_preds.extend(predicted.cpu().numpy())
            all_targets.extend(targets.numpy())

    accuracy = accuracy_score(all_targets, all_preds)
    precision, recall, f1, _ = precision_recall_fscore_support(
        all_targets, all_preds, average='weighted'
    )
    cm = confusion_matrix(all_targets, all_preds)

    return {
        'accuracy': accuracy,
        'precision': precision,
        'recall': recall,
        'f1': f1,
        'confusion_matrix': cm
    }
```

## Training Best Practices

1. **Always use `model.train()` and `model.eval()`** - Affects dropout and batch norm
2. **Zero gradients before backward pass** - Gradients accumulate by default
3. **Move data to device** - Both inputs and targets
4. **Use `torch.no_grad()` during validation** - Saves memory
5. **Save best model based on validation metric** - Not training loss
6. **Use learning rate scheduling** - Improves convergence
7. **Monitor both training and validation metrics** - Detect overfitting
8. **Use gradient clipping for RNNs** - Prevents exploding gradients
9. **Set random seeds for reproducibility** - See [[Main Notes]]
10. **Use mixed precision for large models** - Faster training, less memory

## Related Notes

- [[Main Notes]] - PyTorch fundamentals
- [[Neural Network Modules]] - Building models
- [[Data Loading]] - Dataset and DataLoader
- [[deep_learning]] - Deep learning theory
