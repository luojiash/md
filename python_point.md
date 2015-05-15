#python 知识点

tags: python

---
python 元组的不可变性只适用于元组本身顶层，而并非其内容。
```python
>>> a = (1,[])
>>> a[1].append(1)
>>> a
(1, [1])#内容改变
```