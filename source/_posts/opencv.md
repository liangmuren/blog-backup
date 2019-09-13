---
title: opencv
date: 2019-03-26 20:00:44
tags: python
---

deaplab是将图片按长边resize至513得到的分割结果，当我们需要返回给前端的应该是一个和原图大小一样的结果，所以就要找个办法去处理。分割结果是二维的ndarry，PIL或者SKIMAGE的resize都只能处理图片类型的数组，实验室同学建议我用opencv的resize。

查了一下使用方法，的确可以。代码应该这样写：

```python
seg_map = cv2.resize(seg_map,(width,height))
```

但在resize时会有报错：

> cv2.error: OpenCV(3.4.2) C:\Miniconda3\conda-bld\opencv-suite_1534379934306\work\modules\imgproc\src\resize.cpp:3922: error: (-215:Assertion failed) func != 0 in function 'cv::hal::resize'

之后发现cv2.resize()无法处理int64类型，需要先转换为uint8类型。

```python
seg_map = cv2.resize(seg_map.astype(np.uint8),(width,height))
```

改完之后图片resize回原大小效果正常。