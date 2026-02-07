
#cache #caching #lrucache

## Definition

An **LRU (Least Recently Used) Cache** is a type of cache algorithm that removes the least recently accessed item when the cache reaches its capacity. It is commonly used to manage memory efficiently in operating systems, databases, and applications where frequent data access is needed.

### **How LRU Cache Works**

1. **Capacity Limit**: The cache has a fixed maximum size.
2. **Recent Use Tracking**: When an item is accessed, it is marked as recently used.
3. **Eviction Policy**: If the cache is full and a new item needs to be added, the **least recently used** item is removed.
4. **Fast Access**: Lookup, insertion, and deletion operations should be **efficient** (ideally O(1) time complexity).

In python there is already LRU cache:

```python
from functools import lru_cache

@lru_cache(maxsize=3)
def fib(n):
    if n <= 1:
        return n
    return fib(n-1) + fib(n-2)

print(fib(10))  # Computes Fibonacci numbers with caching
```