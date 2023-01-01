# 基于Python的PIL库实现图像字符化
## 1.原理说明
（1）首先，我们知道一张图片/位图的本质是由一个个像素格子组成的，足够多的不同RGB的像素格子拼在一起就构成了一幅图像。而我们利用python的PIL库的Image模块实现图像字符化便是利用字符替换像素格形成文本，相同或类似的像素就使用同一个字符替换，最终形成字符画。

（2）灰度值 ：指黑白图像中点的颜色深度，范围一般从0-255, 白色为255，黑色为0，故黑白图片也称灰度图像。计算灰度值的简化公式为：**gray=0.2126*r+0.7152*g+0.0722*b**（r，g，b分别是像素的红色、绿色、蓝色RGB值）将彩色的图像转变成灰度图


（3）字符：wmzвгдеёжзийклмнопрсoahkbdpqjftZO0QLCJUYX/\|()1{}[]?-_+~<>i!lI;:,\"^'.
或
$@B%8&WM#*oahkbdpqwmZO0QLCJUYXzcvunxrjft/\|()1{}[]?-_+~<>i!lI;:,\"^`'.
我们就是用这70个字符去等分256个灰度值，完成字符代替像素格。

## 2.代码解释

### 2.1 PIL库（Pillow）
PIL(Python Imaging Library)是一个非常好用的图像处理库，支持在Python中使用。
    `pip install pillow`
```python
import PIL
from PIL import Image
```
 PIL中的Image模块有着许多对图像处理的功能，本次图像符号化利用Image完成。
### 2.2输入路径
ps:此处的图片文件路径与文本文件路径为例子，需根据实际情况输入
```python
# 输入需要转化的图像路径与转换后呈现的文本路径  
imagepath = 'D:\\桌面\\yh.jpg'  
convertpath = 'D:\\桌面\\yh.txt'
```
### 2.3获取图片
 ```python
 img = Image.open(imagepath)  # 打开图片
 #imagepath是图片的路径，字符串类型
```
### 2.4处理图片大小
改变图片的大小使得最终呈现文本的图像更加美观和直观
```python
width, height = img.size  # 赋值初始图片的宽和高  
img = img.resize((int(width * 0.7), int(height * 0.3)))  # 修改成最适合图像的大小
```
### 2.5字符替换函数
get_char()函数实现的功能就是将每个像素格转化为70个字符中对应的那个字符。alpha=0表面图片此处透明，不存在。但这里默认图片存在，即alpha=256。
length得到字符列表的长度，即70；灰度值gray通过简化公式得到；unit是一个常量，可以理解是一个gray值和字符列表索引间的关系的参数。返回值是一个字符。
```python
def get_char(r, g, b, alpha=256):  #  
    if alpha == 0:  
         return " "  
    length = len(asciis)  
    gray = 0.2126 * r + 0.7152 * g + 0.0722 * b   # 简易公式rgb值映射成灰度值得到灰度值  
   # 灰度值的范围是0-255，但asciis字符集只有70  
   # 不同的灰度值对应不同的字符上便能使得图像转化为字符画  
   # 如果灰度值在255的时候，输出的第70个字符超出列表索引,(255+1)可以防止超出索引  
    unit = (255.0 + 1) / length  
    return asciis[int(gray / unit)]  # 返回灰度值对应的字符
```
### 2.6转换函数
convert()函数首先重新获取改变后的图片的宽和高，然后在遍历宽和高，然后通过get_char得到每个像素格对应可替换的字符，加入到字符串texts中，每遍历完图片的一行文本也会换一次行。
遍历完并得到字符文本后，打开文本文件，完成写入文本字符串。打开txt文件，手动缩小后便会呈现字符画。
```python
def convert():  
    width, height = img.size  # 再次赋值改变大小后的宽和高  
  
    texts = ""  
    for row in range(height):  
          for col in range(width):  
              texts += get_char(*img.getpixel((col, row)))  
          texts += "\n"  
  
    with open(convertpath, "w", encoding="utf-8") as file:  
        file.write(texts)
```
# 3.完整代码呈现
```python
from PIL import Image  # 导入Image模块的PIL库  
  
# 输入需要转化的图像路径与转换后呈现的文本路径  
imagepath = 'D:\\桌面\\yh.jpg'  
convertpath = 'D:\\桌面\\yh.txt'  
  
def get_char(r, g, b, alpha=256):  #  
    if alpha == 0:  
        return " "  
    length = len(asciis)  
    gray = 0.2126 * r + 0.7152 * g + 0.0722 * b    # 简易公式rgb值映射成灰度值得到灰度值  
# 灰度值的范围是0-255，但asciis字符集只有70  
# 不同的灰度值对应不同的字符上便能使得图像转化为字符画  
# 如果灰度值在255的时候，输出的第70个字符超出列表索引,(255+1)可以防止超出索引  
    unit = (255.0 + 1) / length  
    return asciis[int(gray / unit)]  # 返回灰度值对应的字符  
  
  
def convert():  
    width, height = img.size  # 再次赋值改变大小后的宽和高  
  
    texts = ""  
    for row in range(height):  
          for col in range(width):  
              texts += get_char(*img.getpixel((col, row)))  
          texts += "\n"  
  
    with open(convertpath, "w", encoding="utf-8") as file:  
         file.write(texts)  
  
if __name__ == '__main__':  
    # 生成字符画所需的字符集  
    asciis=list("wmzвгдеёжзийклмнопрсoahkbdpqjftZO0QLCJUYX/\|()1{}[]?-_+~<>i!lI;:,\"^`'. ")   
    img = Image.open(imagepath)  # 打开图片  
    width, height = img.size  # 赋值初始图片的宽和高  
    img = img.resize((int(width * 0.7), int(height * 0.3)))  # 修改成最适合图像的大小  
  
    convert()  # 调用转换函数
```

# 4.效果演示
## 图1
### 转化前：
![any](https://user-images.githubusercontent.com/116541707/210165544-ec7ff000-ad34-4586-bc00-685be14e2a12.jpg)
### 转化后：
![image](https://user-images.githubusercontent.com/116541707/210165499-8a054067-1677-411f-918c-037d49439183.png)

## 图2

### 转化前：
![yh](https://user-images.githubusercontent.com/116541707/210165535-49610e9b-f4ee-42a4-9d9e-bd6bffe1820a.jpg)

### 转化后：
![image](https://user-images.githubusercontent.com/116541707/210165523-8ca2e060-afb1-40bf-bdc9-431e1e3beb62.png)

# 5.总结
1.本代码功能是完成图像的字符化，但依旧存在一定的局限性，第一是三次元图像的符号化效果会比二次元图像差许多，甚至感官上无法辨认；第二是色彩丰富的图像符号化效果会比色彩相较更单调的图像差。所以本代码主要针对色彩相对不会太花的二次元图像。解决方法暂无，原因是知识量严重不足。

2.本代码参考了[（link）](https://blog.csdn.net/xw1680/article/details/105349156)和来自百度百科的各种有用没用的代码。

3.学无止尽。

### END
