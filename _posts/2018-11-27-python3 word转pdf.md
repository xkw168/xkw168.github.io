---
layout: post
title:  "python3 word转pdf"
date:   2018-11-27
categories: Python
tag: 小技巧
---

* content
{:toc}


&emsp;&emsp;我们可能经常会遇到这样的情况，需要将别人发过来的word文档转为pdf格式，数量少的时候一个个打开另存为即可，但是有时候动不动十几个文件，一个个弄就太麻烦了，最近也遇到了这样的问题，与大家分享一下

---

环境准备：

- python3
- office2007及以上

关键函数：

```python
#doc --> pdf
def doc2pdf(input_file):
    word = Dispatch('Word.Application')
    doc = word.Documents.Open(input_file)
    doc.SaveAs(input_file.replace(".docx", ".pdf"), FileFormat=wdFormatPDF)
    doc.Close()
    word.Quit()
```

```python
#recurse a file
for root, dirs, filenames in walk(directory):
```

完整代码如下（遍历整个文件夹，将所有word文件转为pdf）

```python
from win32com.client import Dispatch
from os import walk

wdFormatPDF = 17


def doc2pdf(input_file):
    word = Dispatch('Word.Application')
    doc = word.Documents.Open(input_file)
    doc.SaveAs(input_file.replace(".docx", ".pdf"), FileFormat=wdFormatPDF)
    doc.Close()
    word.Quit()


if __name__ == "__main__":
    doc_files = []
    directory = "C:\\Users\\xkw\\Desktop\\destData"
    for root, dirs, filenames in walk(directory):
        for file in filenames:
            if file.endswith(".doc") or file.endswith(".docx"):
                doc2pdf(str(root + "\\" + file))

```

**注意**：python3里面没有**win32**这个package，对应的package为**pywin32**，github在[这里](https://github.com/mhammond/pywin32)
