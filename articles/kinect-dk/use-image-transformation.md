---
title: 使用 Azure Kinect 传感器 SDK 图像转换
description: 了解如何使用 Azure Kinect 传感器 SDK 图像转换函数。
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: kinect, azure, 传感器, sdk, 坐标系, 校准, 投影, 取消投影, 转换, rgb-d, 点云
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "73931693"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>使用 Azure Kinect 传感器 SDK 图像转换

遵循特定的函数在 Azure Kinect DK 中的协调相机系统之间使用和转换图像。

## <a name="k4a_transformation-functions"></a>k4a_transformation 函数

 所有带有 *k4a_transformation* 前缀的函数针对整个图像运行。 它们需要通过 [k4a_transformation_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) 获取转换句柄 [k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html)，并通过 [k4a_transformation_destroy()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44) 取消分配. 你还可以参考 SDK [转换示例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)，其中演示了如何使用本主题所述的三个函数。

本文将介绍以下函数：

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>概述

 函数 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 将深度图从深度相机的视点转换为彩色相机的视点。 此函数旨在生成所谓的 RGB-D 图像，其中，D 表示录制深度值的附加图像通道。 在下图中可以看到，[k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 的彩色图像和输出如同它们取自同一视点（即，彩色相机的视点）。

   ![图像转换](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>实现

 此转换函数比单纯针对每个像素调用 [k4a_calibration_2d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) 更为复杂。 它将深度相机几何结构中的三角网格扭曲成彩色相机的几何结构。 使用三角网格可以避免转换的深度图像出现孔洞。 Z 缓冲区确保正确处理遮挡物。 默认已为此函数启用 GPU 加速。

#### <a name="parameters"></a>parameters

 输入参数是转换句柄和深度图像。 深度图像的分辨率必须与创建转换句柄时指定的 ```depth_mode``` 相匹配。 例如，如果转换句柄是使用 1024x1024 ```K4A_DEPTH_MODE_WFOV_UNBINNED``` 模式创建的，则深度图像的分辨率必须是 1024x1024 像素。 输出是需要由用户调用 [k4a_image_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef) 分配的已转换深度图像。 已转换的深度图像的分辨率必须与创建转换句柄时指定的 ```color_resolution``` 相匹配。 例如，如果颜色分辨率设置为 `K4A_COLOR_RESOLUTION_1080P`，则输出图像的分辨率必须是 1920x1080 像素。 输出图像步幅设置为 `width * sizeof(uint16_t)`，因为图像存储 16 位深度值。

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>概述

 函数 [k4a_transformation_depth_image_to_color_camera_custom()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb) 将深度图和自定义图像从深度相机的视点转换为彩色相机的视点。 作为 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 的扩展，此函数旨在生成对应的自定义图像，其中每个像素与除了转换的深度图像之外的彩色相机的相应像素坐标相匹配。

#### <a name="implementation"></a>实现

 此转换函数生成转换的深度图像的方式与 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) 相同。 若要转换自定义图像，此函数提供了使用线性内插或最近的邻域内插的选项。 使用线性内插可以在转换后的自定义图像中创建新值。 使用最近的邻域内插将防止原始图像中不存在的值出现在输出图像中，但会导致图像不太平滑。 自定义图像应为单通道 8 位或 16 位。 默认已为此函数启用 GPU 加速。

#### <a name="parameters"></a>parameters

 输入参数为转换句柄、深度图像、自定义图像和内插类型。 深度图像和自定义图像的分辨率必须与创建转换句柄时指定的 `depth_mode` 相匹配。 例如，如果转换句柄是使用 1024x1024 `K4A_DEPTH_MODE_WFOV_UNBINNED` 模式创建的，则深度图像和自定义图像的分辨率必须是 1024x1024 像素。 `interpolation_type` 应为 `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` 或 `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST`。 输出是转换后的深度图像和转换后的自定义图像，需要由用户通过调用 [k4a_image_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef) 来分配。 转换后的深度图像和转换后的自定义图像的分辨率必须与创建转换句柄时指定的 `color_resolution` 相匹配。 例如，如果颜色分辨率设置为 `K4A_COLOR_RESOLUTION_1080P`，则输出图像的分辨率必须是 1920x1080 像素。 输出深度图像步幅设置为 `width * sizeof(uint16_t)`，因为该图像存储 16 位深度值。 输入的自定义图像和转换后的自定义图像的格式必须为 `K4A_IMAGE_FORMAT_CUSTOM8` 或 `K4A_IMAGE_FORMAT_CUSTOM16`，应相应设置对应的图像步幅。 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>概述

 函数 [k4a_transformation_color_image_to_depth_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29) 将彩色图像从彩色相机的视点转换为深度相机的视点（参阅上图）。 使用此函数可以生成 RGB-D 图像。

#### <a name="implementation"></a>实现

 对于深度图的每个像素，该函数使用像素的深度值来计算彩色图像中的相应子像素坐标。 然后，我们将在彩色图像中的此坐标处查找颜色值。 在彩色图像中执行双线性内插，以获取子像素精度的颜色值。 没有关联的深度读数的像素将分配到输出图像中的 BGRA 值 `[0,0,0,0]`。 默认已为此函数启用 GPU 加速。 由于此方法会在转换的彩色图像中产生孔洞，并且不会处理遮挡物，因此我们建议改用函数 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)。

#### <a name="parameters"></a>parameters

输入参数是转换句柄、深度图像和彩色图像。 深度图像和彩色图像的分辨率必须与创建转换句柄时指定的 depth_mode 和 color_resolution 相匹配。 输出是需要由用户调用 [k4a_image_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef) 分配的已转换彩色图像。 已转换的彩色图像的分辨率必须与创建转换句柄时指定的 depth_resolution 相匹配。 输出图像存储四个 8 位值（表示每个像素的 BGRA）。 因此，图像的步幅为 ```width * 4 * sizeof(uint8_t)```。 数据顺序为像素交错式，即，蓝色值 - 像素 0，绿色值 - 像素 0，红色值 - 像素 0，alpha 值 - 像素 0，蓝色值 - 1，依此类推。

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>概述

函数 [k4a_transformation_depth_image_to_point_cloud()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) 将相机拍摄的 2D 深度图转换为同一相机的坐标系中的 3D 点云。 因此，相机可以是深度相机或彩色相机。

#### <a name="implementation"></a>实现

 该函数的结果与针对每个像素运行 [k4a_calibration_2d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) 的结果相同，不过计算效率更高。 调用 [k4a_transformation_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) 时，我们会预先计算一个所谓的 xy 查找表，用于存储每个图像像素的 x 和 y 比例因子。 调用 [k4a_transformation_depth_image_to_point_cloud()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) 时，我们会通过将像素的 x 比例因子与像素的 Z 坐标相乘，来获取像素的 3D X 坐标。 类似地，与 y 比例因子相乘可以计算出 3D Y 坐标。 SDK 的[快速点云示例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)演示了如何计算 xy 表。 例如，用户可以遵循示例代码实现其自有版本的此函数，以加速其 GPU 管道。

#### <a name="parameters"></a>parameters

 输入参数是转换句柄、相机说明符和深度图像。 如果相机说明符设置为深度，则深度图像的分辨率必须与创建转换句柄时指定的 depth_mode 相匹配。 否则，如果说明符设置为彩色相机，则分辨率必须与所选 color_resolution 的分辨率相匹配。 输出参数是需要由用户调用 [k4a_image_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef) 分配的 XYZ 图像。 该 XYZ 图像的分辨率必须与输入的深度图的分辨率相匹配。 我们将为每个像素存储三个带符号的 16 位坐标值（以毫米为单位）。 因此，XYZ 图像步幅设置为 `width * 3 * sizeof(int16_t)`。 数据顺序为像素交错式，即，X 坐标 – 像素 0，Y 坐标 – 像素 0，Z 坐标 – 像素 0，X 坐标 – 像素 1，依此类推。 如果无法将某个像素转换为 3D，该函数将为该像素分配值 `[0,0,0]`。

## <a name="samples"></a>示例

[转换示例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>后续步骤

了解如何使用 Azure Kinect 传感器 SDK 图像转换函数后，接下来还可以了解

>[!div class="nextstepaction"]
>[Azure Kinect 传感器 SDK 校准函数](use-calibration-functions.md)

此外，可以了解

[坐标系](coordinate-systems.md)
