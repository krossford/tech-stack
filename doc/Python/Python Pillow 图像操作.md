# Python Pillow 图像操作

```python
from PIL import Image

img = Image.open("cat.jpg")

# 图片尺寸
print(img.size)
img.size[0] # 宽度
img.size[1] # 高度

# 操作像素
bitmap = img.load()
bitmap[x, y] = (255, 0, 0)

# 遍历每个像素点
for x in range(img.size[0]):
  for y in range(img.size[1]):
    # do something

# 显示
img.show() 
```

