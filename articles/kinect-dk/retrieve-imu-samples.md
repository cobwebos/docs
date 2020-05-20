---
title: 检索 Azure Kinect IMU 样本
description: 了解如何使用 Azure Kinect SDK 检索 Azure Kinect IMU 示例。
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: kinect, azure, 配置, 深度, 彩色, RBG, 相机, 传感器, sdk, IMU, 运动传感器, 运动, 陀螺仪, 加速度传感器, FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "73932205"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>检索 Azure Kinect IMU 样本

在 Azure Kinect 设备中可以访问惯性运动单元 (IMU)，其中包括加速度传感器和陀螺仪类型。 若要访问 IMU 样本，必须先打开并配置设备，然后捕获 IMU 数据。 有关详细信息，请参阅[查找和打开设备](find-then-open-device.md)。

生成 IMU 样本的频率要比图像高得多。 向主机报告样本的频率低于采样频率。 在等待接收 IMU 样本时，往往已经有多个可用的样本。

有关 IMU 报告频率的详细信息，请参阅 Azure Kinect DK [硬件规格](hardware-specification.md)。

## <a name="configure-and-start-cameras"></a>配置并启动相机

> [!NOTE]
> 仅当彩色和/或深度相机正在运行时，IMU 传感器才能正常工作。 IMU 传感器不能单独工作。

若要启动相机，请使用 [k4a_device_start_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)。

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start cameras\n");
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_start_imu(device))
{
    printf("Failed to start imu\n");
    goto Exit;
}
```

## <a name="access-imu-samples"></a>访问 IMU 样本

 每个 [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details) 包含几乎在同一时间捕获的加速度传感器和陀螺仪读数。

可以在用于获取图像捕获的同一个线程上或者在不同的线程上获取 IMU 样本。

若要在 IMU 样本可用后立即检索它们，可在 [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) 自身的线程上调用该函数。 API 还提供足够的内部队列，使你可以在仅返回每个图像捕获后才检查样本。

由于 IMU 样本存在某种内部队列，你可以在不丢弃任何数据的情况下使用以下模式：

1. 以任意帧速率等待捕获。
2. 处理捕获。
3. 检索所有已排队的 IMU 样本。
4. 重复等待下一个捕获。

若要检索当前已排队的所有 IMU 样本，可以在循环中结合 0 值 `timeout_in_ms` 调用 [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e)，直到函数返回 `K4A_WAIT_RESULT_TIMEOUT`。 `K4A_WAIT_RESULT_TIMEOUT` 指示没有排队的样本，且在指定的超时内没有任何样本抵达。

## <a name="usage-example"></a>用例

```C
k4a_imu_sample_t imu_sample;

// Capture a imu sample
switch (k4a_device_get_imu_sample(device, &imu_sample, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a imu sample\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a imu sample\n");
    goto Exit;
}

// Access the accelerometer readings
if (imu_sample != NULL)
{
    printf(" | Accelerometer temperature:%.2f x:%.4f y:%.4f z: %.4f\n",
            imu_sample.temperature,
            imu_sample.acc_sample.xyz.x,
            imu_sample.acc_sample.xyz.y,
            imu_sample.acc_sample.xyz.z);
}

```

## <a name="next-steps"></a>后续步骤

现在你已了解如何使用 IMU 样本，接下来还可以
>[!div class="nextstepaction"]
>[访问麦克风输入数据](access-mics.md)
