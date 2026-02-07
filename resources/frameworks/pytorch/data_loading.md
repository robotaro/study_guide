#pytorch #data-loading #dataset #dataloader

# PyTorch Data Loading

## Dataset - The Base Class

All datasets should inherit from `torch.utils.data.Dataset`:

```python
from torch.utils.data import Dataset
import torch

class CustomDataset(Dataset):
    def __init__(self, data, labels, transform=None):
        """
        Args:
            data: Your input data (e.g., file paths, numpy arrays)
            labels: Corresponding labels
            transform: Optional transforms to apply
        """
        self.data = data
        self.labels = labels
        self.transform = transform

    def __len__(self):
        """Return the total number of samples"""
        return len(self.data)

    def __getitem__(self, idx):
        """Return a single sample at index idx"""
        sample = self.data[idx]
        label = self.labels[idx]

        if self.transform:
            sample = self.transform(sample)

        return sample, label

# Usage
dataset = CustomDataset(data, labels)
print(f"Dataset size: {len(dataset)}")
sample, label = dataset[0]  # Get first sample
```

## DataLoader - Batching and Shuffling

```python
from torch.utils.data import DataLoader

# Create DataLoader
dataloader = DataLoader(
    dataset,
    batch_size=32,
    shuffle=True,           # Shuffle data every epoch
    num_workers=4,          # Number of subprocesses for data loading
    pin_memory=True,        # Faster transfer to GPU
    drop_last=False         # Drop last incomplete batch
)

# Iterate over batches
for batch_idx, (inputs, targets) in enumerate(dataloader):
    # inputs: [batch_size, ...]
    # targets: [batch_size]
    print(f"Batch {batch_idx}: inputs shape {inputs.shape}")
```

### DataLoader Parameters

- `batch_size`: Number of samples per batch
- `shuffle`: Whether to shuffle data (True for training, False for validation/test)
- `num_workers`: Number of parallel workers for loading data (0 = main process only)
- `pin_memory`: If True, data is loaded into pinned memory for faster GPU transfer
- `drop_last`: If True, drop the last incomplete batch
- `collate_fn`: Custom function to merge list of samples into batch
- `sampler`: Custom sampling strategy
- `persistent_workers`: Keep workers alive between epochs (faster with num_workers > 0)

## Image Dataset Example

```python
from PIL import Image
import os
from torch.utils.data import Dataset
import torchvision.transforms as transforms

class ImageDataset(Dataset):
    def __init__(self, root_dir, transform=None):
        """
        Args:
            root_dir: Directory with all images organized in class folders
            transform: Optional transforms
        """
        self.root_dir = root_dir
        self.transform = transform
        self.images = []
        self.labels = []

        # Assuming structure: root_dir/class_name/image.jpg
        for label, class_name in enumerate(sorted(os.listdir(root_dir))):
            class_path = os.path.join(root_dir, class_name)
            if os.path.isdir(class_path):
                for img_name in os.listdir(class_path):
                    img_path = os.path.join(class_path, img_name)
                    self.images.append(img_path)
                    self.labels.append(label)

    def __len__(self):
        return len(self.images)

    def __getitem__(self, idx):
        img_path = self.images[idx]
        image = Image.open(img_path).convert('RGB')
        label = self.labels[idx]

        if self.transform:
            image = self.transform(image)

        return image, label

# Define transforms
transform = transforms.Compose([
    transforms.Resize((224, 224)),
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.485, 0.456, 0.406],
                        std=[0.229, 0.224, 0.225])
])

# Create dataset and dataloader
dataset = ImageDataset('path/to/data', transform=transform)
dataloader = DataLoader(dataset, batch_size=32, shuffle=True, num_workers=4)
```

## Transforms

### Image Transforms (torchvision.transforms)

```python
import torchvision.transforms as transforms

# Common transforms
transform = transforms.Compose([
    # Resize
    transforms.Resize((256, 256)),
    transforms.Resize(256),  # Resize shorter side to 256

    # Crop
    transforms.CenterCrop(224),
    transforms.RandomCrop(224),
    transforms.RandomResizedCrop(224, scale=(0.8, 1.0)),

    # Flip and rotate
    transforms.RandomHorizontalFlip(p=0.5),
    transforms.RandomVerticalFlip(p=0.5),
    transforms.RandomRotation(degrees=15),

    # Color adjustments
    transforms.ColorJitter(brightness=0.2, contrast=0.2,
                          saturation=0.2, hue=0.1),
    transforms.Grayscale(num_output_channels=3),

    # Convert to tensor (scales to [0, 1])
    transforms.ToTensor(),

    # Normalize (important for pretrained models)
    transforms.Normalize(mean=[0.485, 0.456, 0.406],
                        std=[0.229, 0.224, 0.225]),

    # Random erasing (for regularization)
    transforms.RandomErasing(p=0.5),
])

# Data augmentation for training
train_transform = transforms.Compose([
    transforms.RandomResizedCrop(224),
    transforms.RandomHorizontalFlip(),
    transforms.ColorJitter(0.2, 0.2, 0.2, 0.1),
    transforms.ToTensor(),
    transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225]),
])

# Simple transforms for validation/test
val_transform = transforms.Compose([
    transforms.Resize(256),
    transforms.CenterCrop(224),
    transforms.ToTensor(),
    transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225]),
])
```

### Custom Transforms

```python
class AddGaussianNoise:
    """Add Gaussian noise to tensor"""
    def __init__(self, mean=0., std=1.):
        self.mean = mean
        self.std = std

    def __call__(self, tensor):
        return tensor + torch.randn(tensor.size()) * self.std + self.mean

class Normalize:
    """Normalize to mean=0, std=1"""
    def __init__(self):
        pass

    def __call__(self, tensor):
        mean = tensor.mean()
        std = tensor.std()
        return (tensor - mean) / std

# Use in Compose
transform = transforms.Compose([
    transforms.ToTensor(),
    AddGaussianNoise(0., 0.1),
    Normalize()
])
```

## Built-in Datasets (torchvision)

```python
import torchvision
import torchvision.datasets as datasets

# CIFAR-10
train_dataset = datasets.CIFAR10(
    root='./data',
    train=True,
    download=True,
    transform=transform
)

test_dataset = datasets.CIFAR10(
    root='./data',
    train=False,
    download=True,
    transform=transform
)

# ImageNet
imagenet = datasets.ImageNet(
    root='./data/imagenet',
    split='train',
    transform=transform
)

# MNIST
mnist = datasets.MNIST(
    root='./data',
    train=True,
    download=True,
    transform=transforms.ToTensor()
)

# ImageFolder (for custom datasets with class folders)
dataset = datasets.ImageFolder(
    root='./data/my_dataset',
    transform=transform
)
# Expected structure:
# my_dataset/
#   class1/
#     img1.jpg
#     img2.jpg
#   class2/
#     img3.jpg
```

## Splitting Datasets

### Random Split

```python
from torch.utils.data import random_split

# Split into train/val
dataset = CustomDataset(data, labels)
train_size = int(0.8 * len(dataset))
val_size = len(dataset) - train_size

train_dataset, val_dataset = random_split(dataset, [train_size, val_size])

train_loader = DataLoader(train_dataset, batch_size=32, shuffle=True)
val_loader = DataLoader(val_dataset, batch_size=32, shuffle=False)
```

### Subset for Indices

```python
from torch.utils.data import Subset

# Create subset from specific indices
indices = list(range(0, 1000))
subset = Subset(dataset, indices)
loader = DataLoader(subset, batch_size=32)
```

### Train/Val/Test Split

```python
from torch.utils.data import random_split

dataset = CustomDataset(data, labels)
total_size = len(dataset)
train_size = int(0.7 * total_size)
val_size = int(0.15 * total_size)
test_size = total_size - train_size - val_size

train_dataset, val_dataset, test_dataset = random_split(
    dataset, [train_size, val_size, test_size]
)
```

## Custom Samplers

### Weighted Random Sampler (for imbalanced datasets)

```python
from torch.utils.data import WeightedRandomSampler

# Calculate class weights
class_counts = [500, 100, 50]  # Number of samples per class
class_weights = [1.0 / count for count in class_counts]

# Assign weight to each sample
sample_weights = [class_weights[label] for label in labels]

sampler = WeightedRandomSampler(
    weights=sample_weights,
    num_samples=len(sample_weights),
    replacement=True
)

dataloader = DataLoader(dataset, batch_size=32, sampler=sampler)
# Note: Don't use shuffle=True when using a sampler
```

### Sequential Sampler

```python
from torch.utils.data import SequentialSampler

sampler = SequentialSampler(dataset)
dataloader = DataLoader(dataset, batch_size=32, sampler=sampler)
```

### Subset Random Sampler

```python
from torch.utils.data import SubsetRandomSampler

# Sample from specific indices
indices = list(range(0, 1000))
sampler = SubsetRandomSampler(indices)
dataloader = DataLoader(dataset, batch_size=32, sampler=sampler)
```

## Custom Collate Function

```python
def custom_collate_fn(batch):
    """
    Custom collation for batches with variable-length sequences
    Args:
        batch: List of samples, where each sample is (data, label)
    Returns:
        Batched and processed data
    """
    # Separate data and labels
    data, labels = zip(*batch)

    # Custom processing (e.g., padding sequences)
    from torch.nn.utils.rnn import pad_sequence
    data = pad_sequence(data, batch_first=True, padding_value=0)
    labels = torch.tensor(labels)

    return data, labels

# Use in DataLoader
dataloader = DataLoader(
    dataset,
    batch_size=32,
    collate_fn=custom_collate_fn
)
```

## IterableDataset (for streaming data)

```python
from torch.utils.data import IterableDataset

class StreamingDataset(IterableDataset):
    def __init__(self, data_source):
        super().__init__()
        self.data_source = data_source

    def __iter__(self):
        """Yield samples one at a time"""
        for data in self.data_source:
            # Process data
            yield data

# Usage
dataset = StreamingDataset(data_source)
dataloader = DataLoader(dataset, batch_size=32, num_workers=4)
```

## DataLoader with Multiple Workers Best Practices

```python
# For Windows: wrap DataLoader creation in if __name__ == '__main__'
if __name__ == '__main__':
    # Set start method for multiprocessing (especially on Windows)
    import multiprocessing
    multiprocessing.set_start_method('spawn', force=True)

    train_loader = DataLoader(
        train_dataset,
        batch_size=32,
        shuffle=True,
        num_workers=4,
        pin_memory=True,
        persistent_workers=True  # Keep workers alive between epochs
    )

    for epoch in range(num_epochs):
        for inputs, targets in train_loader:
            # Training code
            pass
```

## Common Data Loading Patterns

### Training with Augmentation, Validation without

```python
train_transform = transforms.Compose([
    transforms.RandomResizedCrop(224),
    transforms.RandomHorizontalFlip(),
    transforms.ToTensor(),
    transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225]),
])

val_transform = transforms.Compose([
    transforms.Resize(256),
    transforms.CenterCrop(224),
    transforms.ToTensor(),
    transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225]),
])

train_dataset = ImageDataset(train_dir, transform=train_transform)
val_dataset = ImageDataset(val_dir, transform=val_transform)

train_loader = DataLoader(train_dataset, batch_size=32, shuffle=True,
                          num_workers=4, pin_memory=True)
val_loader = DataLoader(val_dataset, batch_size=32, shuffle=False,
                        num_workers=4, pin_memory=True)
```

### Caching Data in Memory

```python
class CachedDataset(Dataset):
    def __init__(self, dataset):
        self.dataset = dataset
        self.cache = {}

    def __len__(self):
        return len(self.dataset)

    def __getitem__(self, idx):
        if idx not in self.cache:
            self.cache[idx] = self.dataset[idx]
        return self.cache[idx]
```

## Performance Tips

1. **Use `num_workers > 0`** - Parallel data loading (try 4-8 workers)
2. **Use `pin_memory=True`** - When using GPU
3. **Use `persistent_workers=True`** - With num_workers > 0 to avoid worker recreation
4. **Prefetch data** - DataLoader automatically prefetches batches
5. **Optimize transforms** - Do expensive preprocessing offline when possible
6. **Cache preprocessed data** - For small datasets that fit in memory
7. **Use appropriate batch size** - Larger batches utilize GPU better
8. **Profile data loading** - Use `torch.utils.bottleneck` to identify bottlenecks

## Common Issues

- **Windows multiprocessing**: Wrap DataLoader in `if __name__ == '__main__'`
- **Memory leaks**: Make sure to delete references to old batches
- **Slow data loading**: Increase `num_workers` or optimize transforms
- **Out of memory**: Reduce batch size or use gradient accumulation

## Related Notes

- [[Main Notes]] - PyTorch fundamentals
- [[Training Loop]] - Using DataLoader in training
- [[Neural Network Modules]] - Models that use loaded data
- [[computer_vision]] - Computer vision data loading patterns
