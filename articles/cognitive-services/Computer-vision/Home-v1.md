---
title: Microsoft 认知服务的计算机视觉 API | Microsoft Docs
description: 使用计算机视觉 API 中的高级算法可帮助你处理图像并返回 Microsoft 认知服务中的信息。
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/10/2017
ms.author: kefre
ms.openlocfilehash: 86e0441c600162e479c678d3cb1dbeaad423ddb5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366515"
---
# <a name="what-is-computer-vision-api-version-10"></a>什么是计算机视觉 API 版本 1.0？

> [!IMPORTANT]
> 现已推出计算机视觉 API 的新版本，请参阅：
>- [概述](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [计算机视觉 API 版本 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

使用基于云的计算机视觉 API，开发人员可以访问用于处理图像并返回信息的高级算法。 通过上传图像或指定图像 URL，Microsoft 计算机视觉算法可以基于输入和用户选择以不同方式分析可视内容。 使用计算机视觉 API，用户可以分析图像，以便：
* [基于内容标记图像。](#Tagging)
* [对图像进行分类。](#Categorizing)
* [标识图像的类型和质量。](#Identifying)
* [检测人脸并返回其坐标。](#Faces)
* [识别特定于域的内容。](#Domain-Specific)
* [生成内容说明。](#Descriptions)
* [使用光学字符识别标识图像中找到的打印文本。](#OCR)
* [识别手写的文本。](#RecognizeText)
* [区分配色方案。](#Color)
* [标志成人内容。](#Adult)
* [裁剪要用作缩略图的照片。](#Thumbnails)

## <a name="requirements"></a>要求
* 支持的输入方法：原始图像二进制，采用应用程序/业务流程流或图像 URL 的形式。
* 支持的图像格式：JPEG、PNG、GIF、BMP。
* 图像文件大小：小于 4 MB。
* 图像尺寸：大于 50 x 50 像素。

## <a name="tagging-images"></a>标记图像
计算机视觉 API 在超过 2000 个可识别对象、生物、风景和操作的基础上返回标记。 如果标记含混不清或者不常见，API 响应会提供“提示”，明确已知设置上下文中的标记的含义。 标记未组织为分类，并且不存在继承层次结构。 内容标记的集合构成在完整句子中显示为用户可读语言的图像“说明”的基础。 请注意，此时图像说明仅支持英语。

上载图像或指定图像 URL 后，计算机视觉 API 算法在对象、生物和图像中标识的操作的基础上输出标记。 标记并不局限于主体（如前景中的人员），还包括设置（室内或室外）、家具、工具、工厂、动物、附件、小工具等。

### <a name="example"></a>示例
![House_Yard](./Images/house_yard.jpg) '

```json
Returned Json
{
   'tags':[
      {
         "name":"grass",
         "confidence":0.999999761581421
      },
      {
         "name":"outdoor",
         "confidence":0.999970674514771
      },
      {
         "name":"sky",
         "confidence":999289751052856
      },
      {
         "name":"building",
         "confidence":0.996463239192963
      },
      {
         "name":"house",
         "confidence":0.992798030376434
      },
      {
         "name":"lawn",
         "confidence":0.822680294513702
      },
      {
         "name":"green",
         "confidence":0.641222536563873
      },
      {
         "name":"residential",
         "confidence":0.314032256603241
      },
   ],
}
```
## <a name="categorizing-images"></a>对图像进行分类
除了标记和说明以外，计算机视觉 API 还会返回早期版本中定义的基于分类的类别。 这些类别组织为具有父/子遗传的层次结构的分类。 所有类别均采用英语。 它们可单独使用或与我们的新模型结合使用。

### <a name="the-86-category-concept"></a>86 类别概念
根据下图中所示的包含 86 个概念的列表，可以将图像中找到的视觉功能从广泛到具体进行分类。 有关文本格式的完整分类，请参阅[类别分类](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy)。

![分析类别](./Images/analyze_categories.jpg)

映像                                                  | 响应
------------------------------------------------------ | ----------------
![女士屋顶](./Images/woman_roof.jpg)                 | people
![系列照片](./Images/family_photo.jpg)             | people_crowd
![可爱的小狗](./Images/cute_dog.jpg)                     | animal_dog
![户外山脉](./Images/mountain_vista.jpg)       | outdoor_mountain
![视觉分析食物面包](./Images/bread.jpg)       | food_bread

## <a name="identifying-image-types"></a>标识图像类型
有多种方法可对图像进行分类。 计算机视觉 API 可以设置布尔标志以指示图像是黑白的还是彩色的。 还可以设置一个标志以指示图像是否为线条图。 还可以指示图像是否为剪贴画，并在 0-3 的范围内指示其自身质量。

### <a name="clip-art-type"></a>剪贴画类型
检测图像是否为剪贴画。  

值 | 含义
----- | --------------
0     | 非剪贴画
1     | 不明确
2     | 正常剪贴画
3     | 良好剪贴画

映像|响应
----|----
![视觉分析奶酪剪贴画](./Images/cheese_clipart.jpg)|3 良好剪贴画
![视觉分析住宅庭院](./Images/house_yard.jpg)|0 非剪贴画

### <a name="line-drawing-type"></a>线条图类型
检测图像是否为线条图。

映像|响应
----|----
![视觉分析狮子图](./Images/lion_drawing.jpg)|True
![视觉分析花](./Images/flower.jpg)|False

### <a name="faces"></a>人脸
检测图片内的人脸并生成人脸坐标、人脸边框、性别和年龄。 这些视觉功能是为人脸生成的元数据子集。 要获取为人脸（人脸识别、姿势检测等）生成的更为广泛的元数据，请使用人脸 API。  

映像|响应
----|----
![视觉分析女士屋顶人脸](./Images/woman_roof_face.png) | [ { "age": 23, "gender": "Female", "faceRectangle": { "left": 1379, "top": 320, "width": 310, "height": 310 } } ]
![视觉分析母女人脸](./Images/mom_daughter_face.png) | [ { "age": 28, "gender": "Female", "faceRectangle": { "left": 447, "top": 195, "width": 162, "height": 162 } }, { "age": 10, "gender": "Male", "faceRectangle": { "left": 355, "top": 87, "width": 143, "height": 143 } } ]
![视觉分析系列照片人脸](./Images/family_photo_face.png) | [ { "age": 11, "gender": "Male", "faceRectangle": { "left": 113, "top": 314, "width": 222, "height": 222 } }, { "age": 11, "gender": "Female", "faceRectangle": { "left": 1200, "top": 632, "width": 215, "height": 215 } }, { "age": 41, "gender": "Male", "faceRectangle": { "left": 514, "top": 223, "width": 205, "height": 205 } }, { "age": 37, "gender": "Female", "faceRectangle": { "left": 1008, "top": 277, "width": 201, "height": 201 } } ]


## <a name="domain-specific-content"></a>特定于域的内容

除了标记和顶级分类以外，计算机视觉 API 还支持专用（或特定于域的）信息。 专用信息可作为独立的方法实现或与高级分类一起实现。 该信息用作通过添加特定于域的模型进一步优化 86 类别分类的方法。

目前，唯一支持的专用信息是名人识别和路标识别。 它们为人员和人员组类别以及世界各地的路标进行特定于域的优化。

使用特定于域的模型有以下两个选项：

### <a name="option-one---scoped-analysis"></a>选项 1 - 范围分析
通过调用 HTTP POST 调用仅分析所选模型。 对于此选项，如果你知道你想要使用哪个模型，则指定该模型的名称，将仅收到与该模型相关的信息。 例如，可以使用此选项仅查找名人识别。 响应包含可能匹配的名人列表及其置信度分数。

### <a name="option-two---enhanced-analysis"></a>选项 2 - 增强分析
分析以提供与 86 类别分类中的类别相关的其他详细信息。 当除了一个或多个特定于域的模型中的详细信息以外，用户还希望获取常规图像分析时，可应用此选项。 当调用此方法时，首先会调用 86 类别分类的分类器。 如果任何类别与已知/匹配模型的类别匹配，将执行第二轮分类器调用。 例如，如果“details=all”或“details”包括“celebrities”，则在调用 86 类别分类器后，该方法将调用名人分类器。 结果包含以“people_”开头的标记。

## <a name="generating-descriptions"></a>生成说明 
计算机视觉 API 算法可分析图像中的内容。 此分析构成在完整句子中显示为用户可读语言的“说明”的基础。 该说明概述了图像中找到的内容。 计算机视觉 API 算法可根据图像中标识的对象生成各种说明。 分别对这些说明进行评估并生成置信度分数。 然后将返回置信度分数从高到低的列表。 可以在[此处](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption)找到使用此技术生成图像标题的机器人示例。  

### <a name="example-description-generation"></a>说明生成示例
![黑白建筑](./Images/bw_buildings.jpg) '
```json
 Returned Json

'description':{
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a large city',
         'confidence':0.607638706850331
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a photo of a large city',
         'confidence':0.577256764264197
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a city',
         'confidence':0.538493271791207
      }
   ]   
   'description':[
      "tags":{
         "outdoor",
         "city",
         "building",
         "photo",
         "large",
      }
   ]
}
```

## <a name="perceiving-color-schemes"></a>认知配色方案
计算机视觉算法从图像中提取颜色。 在三种不同的上下文（前景、背景和整体）中分析颜色。 它们可组合为 12 种主要的主题色。 这些主题色为黑色、蓝色、棕色、灰色、绿色、橙色、粉红色、紫色、红色、青色、白色和黄色。 根据图像中的颜色，可能会使用十六进制颜色代码返回简单的黑白或主题色。

映像                                                       | 前景 |背景| 颜色
----------------------------------------------------------- | --------- | ------- | ------
![户外山脉](./Images/mountain_vista.jpg)            | 黑色     | 黑色   | 白色
![视觉分析花](./Images/flower.jpg)               | 黑色     | 白色   | 白色、黑色、绿色
![视觉分析火车站](./Images/train_station.jpg) | 黑色     | 黑色   | 黑色

### <a name="accent-color"></a>主题色
从图像中提取的颜色，设计为通过主色和饱和度的混合向用户呈现最令人大开眼界的颜色。

映像                                                       | 响应
----------------------------------------------------------- | ----
![户外山脉](./Images/mountain_vista.jpg)            | #BC6F0F
![视觉分析花](./Images/flower.jpg)               | #CAA501
![视觉分析火车站](./Images/train_station.jpg) | #484B83


### <a name="black--white"></a>黑白
指示图像是否为黑白的布尔标志。

映像                                                      | 响应
---------------------------------------------------------- | ----
![视觉分析建筑](./Images/bw_buildings.jpg)      | True
![视觉分析住宅庭院](./Images/house_yard.jpg)      | False

## <a name="flagging-adult-content"></a>标志成人内容
各种视觉类别中包含成人和不雅内容，可以检测成人材料检测并限制显示包含性内容的图像。 可以在滑尺上设置成人和不雅内容检测的筛选器以满足用户偏好。

## <a name="optical-character-recognition-ocr"></a>光学字符识别 (OCR)
OCR 技术可检测图像中的文本内容，并将所标识的文本提取到计算机可读的字符流中。 你可以将结果用于搜索和其他多个目的，如医疗记录、安全和银行。 它会自动检测语言。 OCR 可以节省时间，允许用户拍摄文本而非转录文本，从而为用户提供方便。

OCR 支持 25 种语言。 这些语言包括：阿拉伯语、简体中文、繁体中文、捷克语、丹麦语、荷兰语、英语、芬兰语、法语、德语、希腊语、匈牙利语、意大利语、日语、韩语、挪威语、波兰语、葡萄牙语、罗马尼亚语、俄语、塞尔维亚语(西里尔文和拉丁语)、斯洛伐克语、西班牙语、瑞典语和土耳其语。

如果需要，OCR 会围绕图像水平轴更正所识别文本的方向（以度为单位）。 OCR 提供如下图中所示的每个单词的帧坐标。

![OCR 概述](./Images/vision-overview-ocr.png) OCR 的要求：
- 输入图像的大小必须介于 40 x 40 和 3200 x 3200 像素之间。
- 图像不能大于 1000 万像素。

输入图像可以旋转 90 度的任何倍数再加最多 40 度的小角度。

文本识别的准确性取决于图像的质量。 以下情况可能会导致读数不准确：
- 模糊的图像。
- 手写文本或草体文本。
- 艺术字体样式。
- 文本太小。
- 文本上的复杂背景、阴影、炫光或透视变形。
- 单词开头的大写字母过大或缺失
- 下标、上标或删除线文本。

限制：在以文本为主的照片上，误报可能来自部分识别的字。 在某些照片上，尤其在不带任何文本的照片上，精度会因图像类型而存在较大差异。

## <a name="recognize-handwritten-text"></a>识别手写的文本
此技术使你可以检测和提取笔记、信件、文章、白板、表格等对象中的手写文本。它适用于不同的图面和背景，如白纸、黄色便签和白板等。

手写文本识别既省时又省力，并且可通过允许拍摄文本图像，而不必进行转录，使你工作更高效。 它可以数字化说明。 通过数字化可以快速方便地进行搜索。 它还减少了纸张乱堆的情况。

输入要求：
- 支持的图像格式：JPEG、PNG 和 BMP。
- 图像文件大小必须小于 4 MB。
- 图像尺寸必须最小为 40 x 40，最大为 3200 x 3200。

注意：此技术当前处于预览状态，且仅适用于英语文本。

## <a name="generating-thumbnails"></a>生成缩略图
缩略图是完全尺寸的图像的小型表示形式。 不同设备（如手机、平板电脑和电脑）需要不同的用户体验 (UX) 布局和缩略图大小。 使用智能裁剪，此计算机视觉 API 功能可帮助解决该问题。

上载图像后，会生成高质量的缩略图，并且计算机视觉 API 算法会分析图像内的对象。 然后会裁剪图像以满足感兴趣区域 (ROI) 的要求。 输出显示在一个特殊框架中，如下图中所示。 可以使用与原始图像的纵横比不同的纵横比显示生成的缩略图以满足用户需求。

缩略图算法的工作方式如下：

1. 从图像中删除让人分散注意力的元素并识别主对象，即感兴趣区域 (ROI)。
2. 基于所标识的感兴趣区域裁剪图像。
3. 更改纵横比以适应目标缩略图尺寸。

![缩略图](./Images/thumbnail-demo.png)
