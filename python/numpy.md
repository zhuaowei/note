# numpy

## 函数

```python
a1 = numpy.random.randint(0, 100, (2, 3))
a2 = numpy.random.randint(0, 100, (3, 2))
# 产生 2 * 3 的 0 - 100 的随机数
# numpy.random.randn(7, 4)
display(a1, a2)

numpy.dot(a1, a2)
# 矩阵运算

numpy.arange(0, 10 0.1)
numpy.arange(10, 0, -0.1)
# 

# 索引和切片
arr[[2, 3, 4]] # 索引可以是多个
arr[[2, 3, 4]] = 5

arr = numpy.arange(12).reshape(3, 4)
arr[0: 2]
arr[0: 2, 0: 2]	# 多维切片
```

## 布尔索引

```python
names = numpy.array(["bob", "bob", "bob", "john", "john"])
names == "bob"
# >>> array([ True, True, True, False, False ])
names[names == "bob"]
# >>> array(["bob", "bob", "bob"]

# 索引可以用到其他列表，但元素个数要一样
date[names == "bob"]
date[date < 0] = 0
# 小于 0 的 赋值为 0
```

## 求和

```python
# 求和
numpy.sum()

# 横向求和
numpy.sum(a, axis=1)
# 纵向求和
numpy.sum(a, axis=0)
```



