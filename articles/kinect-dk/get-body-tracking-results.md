---
title: 在 Azure Kinect 中获取人体跟踪结果
description: 了解如何使用 Azure Kinect 人体跟踪 SDK 获取人体跟踪结果。
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 传感器, sdk, 人体, 跟踪, 联接
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "76759833"
---
# <a name="get-body-tracking-results"></a>获取人体跟踪结果

人体跟踪 SDK 使用人体跟踪器对象处理 Azure Kinect DK 捕获并生成人体跟踪结果。 它还可以维护跟踪器、处理队列和输出队列的全局状态。 使用人体跟踪器需要执行三个步骤：

- 创建跟踪器
- 使用 Azure Kinect DK 捕获深度图像和 IR 图像
- 将捕获排入队列并弹出结果。

## <a name="create-a-tracker"></a>创建跟踪器


使用人体跟踪的第一步是创建跟踪器，并需要传入传感器校准 [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) 结构。 可以使用 Azure Kinect 传感器 SDK [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) 函数查询传感器校准。

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>捕获深度图像和 IR 图像

[检索图像](retrieve-images.md)页中介绍了如何使用 Azure Kinect DK 捕获图像。

>[!NOTE]
> 为获得最佳性能和准确度，建议使用 `K4A_DEPTH_MODE_NFOV_UNBINNED` 或 `K4A_DEPTH_MODE_WFOV_2X2BINNED` 模式。 不要使用 `K4A_DEPTH_MODE_OFF` 或 `K4A_DEPTH_MODE_PASSIVE_IR` 模式。

Azure Kinect DK [硬件规格](hardware-specification.md)和 [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) 枚举中介绍了支持的 Azure Kinect DK 模式。

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

## <a name="enqueue-the-capture-and-pop-the-results"></a>将捕获排入队列并弹出结果

跟踪器在内部维护一个输入队列和一个输出队列，以便更有效地以异步方式处理 Azure Kinect DK 捕获。 使用 [k4abt_tracker_enqueue_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e) 函数将新的捕获添加到输入队列。 使用 [k4abt_tracker_pop_result()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b) 函数弹出输出队列的结果。 使用的超时值与应用程序相关，控制排队等待时间。

### <a name="real-time-processing"></a>实时处理
对需要实时结果并且可以适应掉帧情况的单线程应用程序使用此模式。 [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) 中的 `simple_3d_viewer` 示例是一个实时处理的示例。

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>同步处理
对不需要实时结果或者无法适应掉帧情况的应用程序使用此模式。

处理吞吐量可能会受限制。

[GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) 中的 `simple_sample.exe` 示例是一个同步处理的示例。

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[访问人体帧中的数据](access-data-body-frame.md)
