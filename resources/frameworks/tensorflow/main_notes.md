From video: https://www.youtube.com/watch?v=IA3WxTTPXqQ&t=5481s
Last timestamp: 3:54:23
### Memory Layout (Column-major vs Row-major)

- **TensorFlow**: By default, TensorFlow follows a row-major order. This means that in a multi-dimensional tensor, the last index changes the fastest. For example, in a 2D array, data is stored row by row.
- **PyTorch**: PyTorch also uses a row-major order for storing tensors, similar to TensorFlow. This is also similar to the order used by NumPy in Python, which makes it a bit more intuitive if you come from a Python/NumPy background.

- IN Tensorflow, tensors are IMMUTABLE BY DEFAULT!