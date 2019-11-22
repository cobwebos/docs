---
title: 使用 Azure Kinect 校准函数
description: 了解如何使用 Azure Kinect DK 的校准函数。
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 传感器, sdk, 坐标系, 校准, 函数, 内部, 外部, 相机, 投影, 取消投影, 转换, rgb-d, 点云
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932202"
---
# <a name="use-azure-kinect-calibration-functions"></a>使用 Azure Kinect 校准函数

使用校准函数可以转换 Azure Kinect 设备上每个传感器的坐标系之间的点。 需要转换整个图像的应用程序可以利用[转换函数](use-image-transformation.md)提供的加速操作。

## <a name="retrieve-calibration-data"></a>检索校准数据

需要检索设备校准数据才能执行坐标系转换。 校准数据存储在 [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) 数据类型中。 该数据是通过 [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) 函数从设备获取的。 校准数据不仅特定于每个设备，而且还特定于相机的工作模式。 因此，[k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) 需要使用 `depth_mode` 和 `color_resolution` 参数作为输入。

### <a name="opencv-compatibility"></a>OpenCV 兼容性

校准参数与 [OpenCV](https://opencv.org/) 兼容。 有关各个相机校正参数的详细信息，另请参阅 [OpenCV 文档](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c)。 另请参阅 SDK 的 [OpenCV 兼容性示例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)，其中演示了 [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) 类型与相应 OpenCV 数据结构之间的转换。

## <a name="coordinate-transformation-functions"></a>坐标转换函数

下图显示了 Azure Kinect 的不同坐标系，以及用于在这些坐标系之间转换的函数。 为了使插图显得简洁，我们省略了陀螺仪和加速度传感器的 3D 坐标系。

   ![坐标转换](./media/how-to-guides/coordinate-transformation.png)

有关镜头失真的备注：2D 坐标始终引用 SDK 中失真的图像。 SDK 的[失真矫正示例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)演示了图像失真矫正。 一般情况下，3D 点永远不会受到镜头失真的影响。

### <a name="convert-between-3d-coordinate-systems"></a>在 3D 坐标系之间转换

函数 [k4a_calibration_3d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) 使用相机的外部校准将源坐标系的 3D 点转换为目标坐标系的 3D 点。 源和目标可设置为四个 3D 坐标系中的任何一个，即，彩色相机、深度相机、陀螺仪或加速度传感器。 如果源和目标相同，则返回未修改的输入 3D 点作为输出。

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>在 2D 与 3D 坐标系之间转换

函数 [k4a_calibration_3d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) 将源坐标系的 3D 点转换为目标相机的 2D 像素坐标。 此函数通常称为投影函数。 尽管源可以设置为四个 3D 坐标系中的任何一个，但目标必须是深度相机或彩色相机。 如果源和目标不同，则会使用 [k4a_calibration_3d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) 将输入的 3D 点转换为目标相机的 3D 坐标系。 以目标相机坐标系表示 3D 点后，将使用目标相机的内部校准计算相应的 2D 像素坐标。 如果 3D 点超出了目标相机的可视区域，则有效值将设置为 0。

函数 [k4a_calibration_2d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) 将源相机的 2D 像素坐标转换为目标相机坐标系的 3D 点。 源必须是彩色相机或深度相机。 目标可设置为四个 3D 坐标系中的任何一个。 除了 2D 像素坐标外，源相机图像中像素的深度值（以毫米为单位）也需要作为函数的输入，在彩色相机几何中推导出深度值的一种方法是使用函数 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)。 该函数使用源相机的内部校准通过指定的像素坐标计算源相机焦点引出的 3D 射线。 然后，使用深度值查找此射线的确切 3D 点位置。 此操作通常称为取消投影函数。 如果源和目标相机不同，该函数会通过 [k4a_calibration_3d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) 将 3D 点转换为目标的坐标系。 如果 2D 像素坐标超出了源相机的可视区域，则有效值将设置为 0。

### <a name="converting-between-2d-coordinate-systems"></a>在 2D 坐标系之间转换

函数 [k4a_calibration_2d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) 将源相机的 2D 像素坐标转换为目标相机的 2D 像素坐标。 源和目标必须设置为彩色相机或深度相机。 该函数需要将源相机图像中像素的深度值（以毫米为单位）作为输入，在彩色相机几何中推导出深度值的一种方法是使用函数 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)。 它将调用 [k4a_calibration_2d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) 转换为源相机系统的 3D 点。 然后，它将调用 [k4a_calibration_3d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) 转换为目标相机图像的 2D 像素坐标。 如果 [k4a_calibration_2d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) 或 [k4a_calibration_3d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) 返回无效的结果，则有效值将设置为 0。

## <a name="related-samples"></a>相关示例

- [OpenCV 兼容性示例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [失真矫正示例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [快速点云示例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>后续步骤

了解相机校正后，接下来还可以了解如何
>[!div class="nextstepaction"]
>[捕获设备同步](capture-device-synchronization.md)

此外，可以了解

[坐标系](coordinate-systems.md)
