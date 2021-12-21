# selenium避坑

#### 安装 selenium

```bash
pip install selenium
# 或
conda install selenium
```

#### 解决问题

##### 代码

```tex
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome()
driver.get("http://www.python.org")
assert "Python" in driver.title
elem = driver.find_element_by_name("q")
elem.clear()
elem.send_keys("pycon")
elem.send_keys(Keys.RETURN)
assert "No results found." not in driver.page_source
driver.close()
```

```bash
# 运行
python <filename>
```

直接运行会报错 [系统找不到指定的文件]

##### 解决：(三个问题)

1. chromedriver

> http://npm.taobao.org/mirrors/chromedriver/
>
> 找到与自己chrome版本相同的chromedriver，下载、解压、移动

添加到path环境变量中

> 例如：
>
> C:\Program Files (x86)\Google\Chrome\Application

2. shell

报错提示会有个 subprocess.py 的文件，点进去有个一大堆变量的\_\_init__的函数，把 <code>shell=False </code> 改为 <code>True</code>.

然后运行还会报错。

3. 指定chromedriver的路径

把 <code>driver = webdriver.Chrome()</code> 改为 <code>driver = webdriver.Chrome("C:\\Program Files (x86)\\Google\\Chrome\\Application\\chromedriver.exe")</code>

最后执行就没有问题了

#### 代码解释

```python
# 支持浏览器
from selenium import webdriver
# 支持键盘按键
from selenium.webdriver.common.keys import Keys

# 指定 chromedriver 的路径 必须
# 创建实例
driver = webdriver.Chrome("C:\\Program Files (x86)\\Google\\Chrome\\Application\\chromedriver.exe")

# 打开网页，加载完毕后执行下一句（可能不知道什么时候停止）
driver.get("http://www.python.org")

assert "Python" in driver.title

# 通过名字查找元素，还有其他方法
elem = driver.find_element_by_name("q")

# 清空输入框的内容
elem.clear()

# 使用键盘输入，然后回车
elem.send_keys("pycon")
elem.send_keys(Keys.RETURN)
assert "No results found." not in driver.page_source
# 关闭
driver.close()
```

