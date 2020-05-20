---
title: 检索 Azure Kinect 图像数据
description: 了解如何使用 Kinect 传感器 SDK 检索 Azure Kinect 图像数据。
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 检索, 传感器, 相机, sdk, 深度, rgb, 图像, 颜色, 捕获, 分辨率, 缓冲区
ms.openlocfilehash: 84e678993f94c17bf58fb134234afaee4139aad5
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "73931697"
---
# <a name="retrieve-azure-kinect-image-data"></a>检索 Azure Kinect 图像数据

本页提供有关如何通过 Azure Kinect 检索图像的详细信息。 本文将演示如何捕获和访问在彩色和深度相机设备之间协调的图像。 若要访问图像，必须先打开并配置设备，然后可以捕获图像。
在配置和捕获图像之前，必须[找到并打开设备](find-then-open-device.md)。

你还可以参考 [SDK 流示例](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming)，其中演示了如何使用本文所述的函数。

本文将介绍以下函数：

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>配置并启动设备

Kinect 设备上提供的两个相机支持多种模式、分辨率和输出格式。 有关完整列表，请参考 Azure Kinect 开发工具包[硬件规格](hardware-specification.md)。

流配置是使用 [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 结构中的值设置的。

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;
config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start device\n");
    goto Exit;
}
```

相机启动后，它们将不断捕获数据，直到已调用 [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) 或设备关闭。

## <a name="stabilization"></a>稳定化

使用多设备同步功能启动设备时，我们强烈建议使用固定的曝光设置。
如果设置手动曝光，最多可能需要在设备上拍摄 8 次，图像和帧速率就会才能稳定。 如果使用自动曝光，最多可能需要拍摄 20 次，图像和帧速率才能稳定。

## <a name="get-a-capture-from-the-device"></a>从设备获取捕获

图像是以关联的方式从设备捕获的。 捕获的每个图像包含深度图像、IR 图像、彩色图像或图像的组合。

默认情况下，API 只会在收到流模式请求的所有图像后才返回捕获。 可以通过清除 [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 的 [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) 参数，将 API 配置为在深度图像和彩色图像可用后，立即返回仅包含这些图像的部分捕获。

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

在 API 成功返回捕获后，当你用完捕获对象时，必须调用 [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)。

## <a name="get-an-image-from-the-capture"></a>从捕获中获取图像

若要检索捕获的图像，请针对每个图像类型调用相应的函数。 可取值为：

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

用完图像后，必须对这些函数返回的任何 [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) 句柄调用 [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)。

## <a name="access-image-buffers"></a>访问图像缓冲区

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) 提供多个访问器函数用于获取图像的属性。

若要访问图像的内存缓冲区，请使用 [k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)。

以下示例演示如何访问捕获的深度图像。 相同的原则也适用于其他图像类型。 但是，请务必将图像类型变量替换为正确的图像类型，例如 IR 或彩色。

```C
// Access the depth16 image
k4a_image_t image = k4a_capture_get_depth_image(capture);
if (image != NULL)
{
    printf(" | Depth16 res:%4dx%4d stride:%5d\n",
            k4a_image_get_height_pixels(image),
            k4a_image_get_width_pixels(image),
            k4a_image_get_stride_bytes(image));

    // Release the image
    k4a_image_release(image);
}

// Release the capture
k4a_capture_release(capture);
```

## <a name="next-steps"></a>后续步骤

既然你已了解如何捕获和协调相机的彩色图像与深度图像，现在可以使用 Azure Kinect 设备。 你还可以：

>[!div class="nextstepaction"]
>[检索 IMU 样本](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[访问麦克风](access-mics.md)
