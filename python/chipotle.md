# chipotle

## 引入

```python
import chipotle as pd

# 文件路径
path = ""

# 读取数据
df = pd.read_csv(path, "\t")

# 查看数据结构
df.shape
#>>> (555, 6)

# 查看列
df.shape[1]

# 打印全部列的名称
df.colmnus
df.index
RangeIndex(start = , stop = ,step = )
df["item_name"]
df["item_name"].value_counts(ascending=True).head()
df["item_name"].value_counts(ascending=True).head(1)[0]

df["item_name"].unique()# 罗列
df["item_name"].nunique()# 不重复

df["choich_description"].value_counts(ascending=True).head()

df["quantity"].sum()# 求和
df["item_price"].sum()
df.info()# 显示数据类型

checkPrice = lambda x:float(x[1:])
# 转化为浮点型
df["item_price"] = df["item_price"].apply(checkPrice)
df.head()
df.item_price.sum()
df["sub_total"] = df["item_price"] * df["quantity"]
df.sub_total.sum()

# 
df["order_id"].nunique()

```

```python
# 转化为浮点数
checkPrice=lambda x:float(x[1:])
df["item_price"]=df["item_price"].apply(chevkPrice)

df.head()

# 分组求和
df["sub_total"]=df["quantity"]*df["item_price"]
df.groupby(by="order_id").count()	# 分组

df.groupby(by="order_id")["sub_totle"].sum()# 每组总和
df.groupby(by="order_id")["sub_totle"].mean()# 每组平均
df.groupby(by="order_id")["sub_totle"].max()# 每组最大
df.groupby(by="order_id")["sub_totle"].min()
```

## 关联

```python
pd.merge(d1, d2, on="dept")
pd.merge(d1, d2, left_on="employee", right_on="employee"[, how="outer"])
# how: 关联方式 外关联 全关联，并集
# 内关联: 交集 
# 外关联中的左关联 left
# 外关联中的右关联 right
```

```python
****.applymap(lambda x:round(2))	# 保留两位小数
```

