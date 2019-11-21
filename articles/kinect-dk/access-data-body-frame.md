---
title: 访问人体帧中的 Azure Kinect DK 数据
description: 了解正文框中的数据以及用于访问 Azure Kinect DK 数据的函数。
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: how-to
keywords: 人体, 帧, azure, kinect, 跟踪, 提示
ms.openlocfilehash: 63236725c0829d58335143b26ee6d16d2f8f8819
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931768"
---
# <a name="access-data-in-body-frame"></a>访问人体帧中的数据

本文介绍正文框架中包含的数据，以及用于访问这些数据的函数。

本文将介绍以下函数：

- [k4abt_frame_get_body_id()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_ga673def22c3e3d4683a5702d8fe3cdc5e.html#ga673def22c3e3d4683a5702d8fe3cdc5e)
- [k4abt_frame_get_body_index_map()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6)
- [k4abt_frame_get_body_skeleton()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_ga7ef3f65827c9d21dac0caef572cc6c23.html#ga7ef3f65827c9d21dac0caef572cc6c23)
- [k4abt_frame_get_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a)
- [k4abt_frame_get_num_bodies()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_ga485b3a0f29e61c75f2e3d8b78b345ea9.html#ga485b3a0f29e61c75f2e3d8b78b345ea9)
- [k4abt_frame_get_device_timestamp_usec()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_ga04be7b814b40296cd6b97044ed7283e4.html#ga04be7b814b40296cd6b97044ed7283e4)

## <a name="key-components-of-a-body-frame"></a>人体帧的关键组成部分

每个人体帧包含人体结构的集合、2D 人体索引映射，以及生成了此结果的输入捕获。

![人体帧的组成部分](./media/how-to-guides/body-frame.png)

## <a name="access-the-collection-of-body-structs"></a>访问人体结构的集合

在单个捕获中可能会检测到多个人体。 可以调用 [k4abt_frame_get_num_bodies()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_ga485b3a0f29e61c75f2e3d8b78b345ea9.html#ga485b3a0f29e61c75f2e3d8b78b345ea9) 函数查询人体数目。

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
```

使用 [k4abt_frame_get_body_id()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_ga673def22c3e3d4683a5702d8fe3cdc5e.html#ga673def22c3e3d4683a5702d8fe3cdc5e) 和 [k4abt_frame_get_body_skeleton()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_ga7ef3f65827c9d21dac0caef572cc6c23.html#ga7ef3f65827c9d21dac0caef572cc6c23l) 函数可以循环访问每个人体索引，以查找人体 ID 和关节位置/方向信息。

```C
for (size_t i = 0; i < num_bodies; i++)
{
    k4abt_skeleton_t skeleton;
    k4abt_frame_get_body_skeleton(body_frame, i, &skeleton);
    uint32_t id = k4abt_frame_get_body_id(body_frame, i);
}
```

## <a name="access-the-body-index-map"></a>访问人体索引映射

使用 [k4abt_frame_get_body_index_map()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6) 函数可以访问人体索引映射。 有关人体索引映射的详细说明，请参阅[人体索引映射](body-index-map.md)。 不再需要人体索引映射时，请务必将其释放。

```C
k4a_image_t body_index_map = k4abt_frame_get_body_index_map(body_frame);
...  // Do your work with the body index map
k4a_image_release(body_index_map);
```

## <a name="access-the-input-capture"></a>访问输入捕获

人体跟踪器是一个异步 API。 弹出结果时，可能已释放原始捕获。 使用 [k4abt_frame_get_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a) 函数可以查询用于生成此人体跟踪结果的输入捕获。 每次调用此函数，k4a_capture_t 的引用计数就会递增。 不再需要捕获时，请使用 [k4a_capture_release()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) 函数。

```C
k4a_capture_t input_capture = k4abt_frame_get_capture(body_frame);
... // Do your work with the input capture
k4a_capture_release(input_capture);
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[Azure Kinect 人体跟踪 SDK](https://microsoft.github.io/Azure-Kinect-Body-Tracking/)
