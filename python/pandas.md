

# pandas

数据处理模块，针对二维数据

## 引入

```python
import pandas as pd
from pandas import Series, DataFrame

date = {"": [], "": []}
df = DataFrame(data)
```

## 指定表头和索引

```python
df = DataFrame(date,
              columns=["city", "year", "price", "dept"],
              index=["one", "two", "three", "4"])
```

## 取值

```python
df[["city", "year"]]
```

## 赋值

``` python
df["step"] = [1, 2, 3, 4, 5, 6]	# 个数一致
```

## 切片

```python
df.loc[["one", "two"]]
df.loc[["one", "two"], ["city", "year", "price"]]
df.loc
```

