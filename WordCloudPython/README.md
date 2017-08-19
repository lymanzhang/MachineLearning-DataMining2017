## wordcloud python
## python中的wordcloud实现


![parrotWordCloud](https://github.com/amueller/word_cloud/raw/master/examples/parrot.png)

首先导入一些需要用到的模块

```python
import os
import numpy as np
import matplotlib.pyplot as plt
from PIL import Image
import jieba
from wordcloud import WordCloud,ImageColorGenerator

```

上面三个就不讲了，`PIL`是`Python`的图像处理标准库，现在可以直接安装`Pillow`,想要了解更多的话请移步[廖雪峰老师的相关教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014320027235877860c87af5544f25a8deeb55141d60c5000)。剩下的`jieba`是我们今天要用做分词器用的，`wordcloud`自然就是制作词云的模块了。

首先，导入我们的文本文件，这里选用的是朱自清创作散文[《背影》](http://www.ccview.net/htm/xiandai/zzq/zzqsw003.htm)，在读取之前可以使用`NotePad++`等软件手工把文本转为`utf-8`格式，防止出现编码错误。

```python
# 工作目录
cwd = os.getcwd()
# 读取文件
fh = open(cwd+'\\txt\\背影.txt','rb')
data = fh.read().decode('utf-8')
```

然后，我们就要使用`jieba`做中文的分词了。`jieba(结巴分词)`是现在最好用的`python`下中文分词模块之一，具有中文分词、添加自定义词典、关键词提取和词性标注等多种功能，我们这里只使用它的分词功能对其他功能和分词的算法不作探讨。

结巴最常用的分词方法是`jieba.cut`，该方法接受两个输入参数:

- 第一个参数为需要分词的字符串

- `cut_all`参数用来控制采用全模式还是精确模式，默认采用精确模式

```python
seg_str = ' '.join(jieba.cut(data,cut_all = False))
```

这一句代码，就实现了对文章的分词，返回的是一个字符串。

在进行下一步之前，可以简单地看看它的词频情况，也不对数据进行清洗了
```python
from collections import Counter
seg_list = jieba.cut(data,cut_all = False)
count = Counter(seg_list)
print(count)
```

字典很长，这里就不打印出来了。

下面，到了最重要的部分了，要开始制作我们的词云了。下面对`wordcloud`模块做一个简单介绍，有兴趣的同学可以`help(wordcloud)`查看完整的官方帮助文档。

在`wordcloud`模块有两个`object`:

- `wordcloud.color_from_image.ImageColorGenerator`

- `wordcloud.wordcloud.WordCloud`

第一个类`wordcloud.color_from_image.ImageColorGenerator`是根据输入的图像`image`得到一张给你的词上色的颜色图，其中输入的图像`image`的格式是 `nd-array.shape =(height, width, 3)`,只使用`RGB`三个通道，`alpha`透明度通道会自动忽略。

第二个类`wordcloud.wordcloud.WordCloud`就是用来设置相关参数来实现词云的了，由于输入参数太多，这里只挑出常用的一些进行介绍

- `font_path` :使用的字体路径，可以直接把ttf或者otf直接贴到工作目录下，使用的字体只有支持中文才可以在词图中显示中文

- `width` : 宽 int (default=400)

- `height` :高 int (default=200)

- `prefer_horizontal` : 旋转角度 float (default=0.90)

- `min_font_size` : 最小字体 int (default=4)

- `max_words` : 最大词数 number (default=200)

- `background_color` : 背景颜色 color value (default=”black”)

- `max_font_size` : 最大字体尺寸 int or None (default=None)

- `regexp` : 正则表达式 用来分词，默认使用r”\w[\w’]+”

这里有一个参数`mask`放在最后讲，这个参数就是用来设置词云的外边框的，格式要求和上面的`image`是一致的，它会把白色的部分`(RGB#FFFFFF)`的部分“去掉”，保留剩下的部分做词云，所以符合要求的图片像下面一样。

懒得找图片或者编辑图片了，方便起见直接就使用上面的这张Alice就好了，有兴趣的同学可以找些图片，用`PIL`处理下。

```python
im = np.array(Image.open(cwd+'\\image\\alice_color.png'))
#Generate WordCloud
wc = WordCloud(background_color = "white",
               max_words = 100,
               mask = im,
               max_font_size = 80,
               font_path = 'msyh.ttf')
wc.generate(seg_str)
# 从背景图片得到颜色
imColor = ImageColorGenerator(im)  

# 使用jupyter notebook使用，否则请注释掉
% matplotlib inline
plt.imshow(wc)
plt.axis("off")
plt.figure()
plt.imshow(wc.recolor(color_func=imColor))
plt.axis("off")
plt.figure()
```

最后，把所有的代码拼起来运行一下，就可以得到如下的两张不同颜色的中文词云图。
![beiying_new](https://github.com/lymanzhang/MachineLearning-DataMining2017/blob/master/WordCloudPython/image/beiying_new.PNG)

参考资料来源：

[结巴首页](https://www.oschina.net/p/jieba)

[wordcloud官方](https://amueller.github.io/word_cloud/index.html)
