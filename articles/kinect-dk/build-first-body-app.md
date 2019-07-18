---
title: 快速入门 - 生成第一个 Azure Kinect 人体跟踪应用程序
description: 有关生成第一个 Azure Kinect 人体跟踪应用程序的分步说明
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, 传感器, sdk, 人体, 跟踪, 关节, 应用程序, 第一个
ms.openlocfilehash: 9d7affd68219021cea99494c7e26e43dd4e86cb8
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876643"
---
# <a name="quickstart-build-your-first-azure-kinect-body-tracking-application"></a>快速入门：生成第一个 Azure Kinect 人体跟踪应用程序

想要开始使用人体跟踪 SDK？ 本快速入门可帮助你启动并运行人体跟踪！

## <a name="prerequisites"></a>先决条件

- [设置 Azure Kinect DK](set-up-azure-kinect-dk.md)
- [设置人体跟踪 SDK](body-sdk-setup.md)
- 已完成有关[生成第一个 Azure Kinect 应用程序](build-first-app.md)的快速入门。
- 熟悉以下传感器 SDK 函数：
  - [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
  - [k4a_device_start_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dc81cbeb54b07e4bbb7d639c448f6eb.html#ga4dc81cbeb54b07e4bbb7d639c448f6eb)
  - [k4a_device_stop_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  - [k4a_device_close()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)
- 查看有关以下人体跟踪 SDK 函数的文档：
  - [k4abt_tracker_create()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_gae395d73880395ffc6ffbdd5132aa9181.html#gae395d73880395ffc6ffbdd5132aa9181)
  - [k4abt_tracker_enqueue_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e)
  - [k4abt_tracker_pop_result()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b)
  - [k4abt_tracker_shutdown()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_ga94036969ef94cbc414c78b3f6d04bfa5.html#ga94036969ef94cbc414c78b3f6d04bfa5)
  - [k4abt_tracker_destroy()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_gab2c9afca092130976cd66077c0557ed1.html#gab2c9afca092130976cd66077c0557ed1)

## <a name="headers"></a>标头

人体跟踪使用单个标头 `k4abt.h`。 请包含此标头以及 `k4a.h`。 确保所选的编译器已针对传感器 SDK 和人体跟踪 SDK `lib` 与 `include` 文件夹进行设置。 还需要链接到 `k4a.lib` 和 `k4abt.lib` 文件。 运行该应用程序需要 `k4a.dll`、`k4abt.dll`、`onnxruntime.dll` 和 `dnn_model.onnx` 位于应用程序执行路径中。

```C
#include <k4a/k4a.h>
#include <k4abt.h>
```

## <a name="open-device-and-start-the-camera"></a>打开设备并启动相机

第一个人体跟踪应用程序假设已将单个 Azure Kinect 设备连接到电脑。

人体跟踪基于传感器 SDK。 若要使用人体跟踪，首先需要打开并配置设备。 使用 [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) 函数打开设备，然后使用 [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 对象对其进行配置。 为获得最佳结果，请将深度模式设置为 `K4A_DEPTH_MODE_NFOV_UNBINNED` 或 `K4A_DEPTH_MODE_WFOV_2X2BINNED`。 如果深度模式设置为 `K4A_DEPTH_MODE_OFF` 或 `K4A_DEPTH_MODE_PASSIVE_IR`，人体跟踪器将无法运行。

在[此页](find-then-open-device.md)上可以找到有关查找和打开设备的详细信息。

可在以下页面上找到有关 Azure Kinect 深度模式的详细信息：[硬件规范](hardware-specification.md)和 [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) 枚举。

```C
k4a_device_t device = nullptr;
k4a_device_open(0, &device);

// Start camera. Make sure depth camera is enabled.
k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
deviceConfig.depth_mode = K4A_DEPTH_MODE_WFOV_2X2BINNED;
deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
k4a_device_start_cameras(device, &deviceConfig);
```

## <a name="create-the-tracker"></a>创建跟踪器

获取人体跟踪结果的第一步是创建人体跟踪器。 该跟踪器需要 [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) 传感器校准结构。 可以使用 [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) 函数查询传感器校准。

```C
k4a_calibration_t sensor_calibration;
k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration);

k4abt_tracker_t tracker = NULL;
k4abt_tracker_create(&sensor_calibration, &tracker);
```

## <a name="get-captures-from-the-azure-kinect-device"></a>从 Azure Kinect 设备获取捕获

在[此页](retrieve-images.md)上可以找到有关检索图像数据的详细信息。

```C
// Capture a depth frame
k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS);
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>将捕获排入队列并弹出结果

跟踪器在内部维护一个输入队列和一个输出队列，以便更有效地以异步方式处理 Azure Kinect DK 捕获。 下一步是使用 `k4abt_tracker_enqueue_capture()` 函数将新的捕获添加到输入队列。 使用 `k4abt_tracker_pop_result()` 函数弹出输出队列的结果。 超时值与应用程序相关，控制排队等待时间。

第一个人体跟踪应用程序使用实时处理模式。 有关其他模式的详细说明，请参阅[获取人体跟踪结果](get-body-tracking-results.md)。

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

## <a name="access-the-body-tracking-result-data"></a>访问人体跟踪结果数据

每个传感器捕获的人体跟踪结果存储在人体帧 [k4abt_frame_t](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/structk4abt__frame__t.html) 结构中。 每个人体帧包含三个重要组成部分：人体结构的集合、2D 人体索引映射和输入捕获。

第一个人体跟踪应用程序只访问检测到人体数。 有关人体帧中的数据的详细说明，请参阅[访问人体帧中的数据](access-data-body-frame.md)。

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
printf("%zu bodies are detected!\n", num_bodies);
```

## <a name="clean-up"></a>清理

最后一步是关闭人体跟踪器并释放人体跟踪对象。 此外，还需要停止并关闭设备。

```C
k4abt_tracker_shutdown(tracker);
k4abt_tracker_destroy(tracker);
k4a_device_stop_cameras(device);
k4a_device_close(device);
```

## <a name="full-source"></a>完整源代码

```C
#include <stdio.h>
#include <stdlib.h>

#include <k4a/k4a.h>
#include <k4abt.h>

#define VERIFY(result, error)                                                                            \
    if(result != K4A_RESULT_SUCCEEDED)                                                                   \
    {                                                                                                    \
        printf("%s \n - (File: %s, Function: %s, Line: %d)\n", error, __FILE__, __FUNCTION__, __LINE__); \
        exit(1);                                                                                         \
    }                                                                                                    \

int main()
{
    k4a_device_configuration_t device_config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    device_config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

    k4a_device_t device;
    VERIFY(k4a_device_open(0, &device), "Open K4A Device failed!");
    VERIFY(k4a_device_start_cameras(device, &device_config), "Start K4A cameras failed!");

    k4a_calibration_t sensor_calibration;
    VERIFY(k4a_device_get_calibration(device, device_config.depth_mode, device_config.color_resolution, &sensor_calibration),
        "Get depth camera calibration failed!");

    k4abt_tracker_t tracker = NULL;
    VERIFY(k4abt_tracker_create(&sensor_calibration, &tracker), "Body tracker initialization failed!");

    int frame_count = 0;
    do
    {
        k4a_capture_t sensor_capture;
        k4a_wait_result_t get_capture_result = k4a_device_get_capture(device, &sensor_capture, K4A_WAIT_INFINITE);
        if (get_capture_result == K4A_WAIT_RESULT_SUCCEEDED)
        {
            frame_count++;
            k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
            k4a_capture_release(sensor_capture);
            if (queue_capture_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                // It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Add capture to tracker process queue timeout!\n");
                break;
            }
            else if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
            {
                printf("Error! Add capture to tracker process queue failed!\n");
                break;
            }

            k4abt_frame_t body_frame = NULL;
            k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
            if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
            {
                size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
                printf("%zu bodies are detected!\n", num_bodies);

                k4abt_frame_release(body_frame);
            }
            else if (pop_frame_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                //  It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Pop body frame result timeout!\n");
                break;
            }
            else
            {
                printf("Pop body frame result failed!\n");
                break;
            }
        }
        else if (get_capture_result == K4A_WAIT_RESULT_TIMEOUT)
        {
            // It should never hit time out when K4A_WAIT_INFINITE is set.
            printf("Error! Get depth frame time out!\n");
            break;
        }
        else
        {
            printf("Get depth capture returned error: %d\n", get_capture_result);
            break;
        }

    } while (frame_count < 100);

    printf("Finished body tracking processing!\n");

    k4abt_tracker_shutdown(tracker);
    k4abt_tracker_destroy(tracker);
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[获取人体跟踪结果](get-body-tracking-results.md)
