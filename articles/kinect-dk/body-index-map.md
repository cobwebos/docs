---
title: Azure Kinect 人体索引映射
description: 使用人体索引映射
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 移植, 人体, 跟踪, 索引, 分段, 映射
ms.openlocfilehash: b873ad7f811c0552e4241751c1624677ef59f6b7
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876644"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Azure Kinect 人体跟踪索引映射

人体索引映射包括深度机捕捉中每个人体的实例分段图。 每个像素映射到深度或 IR 图像中的相应像素。 每个像素的值表示该像素所属的人体。 它可以是背景（值 `K4ABT_BODY_INDEX_MAP_BACKGROUND`）或检测到的 `k4abt_body_t` 的索引。

![人体索引映射示例](./media/concepts/body-index-map.png)

>[!NOTE]
> 人体索引不同于人体 ID。可以调用 API [k4abt_frame_get_body_id()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/0.9.x/group__btfunctions_ga673def22c3e3d4683a5702d8fe3cdc5e.html#ga673def22c3e3d4683a5702d8fe3cdc5e) 从给定的人体索引中查询人体 ID。


## <a name="using-body-index-map"></a>使用人体索引映射

人体索引映射存储为 `k4a_image_t`，使用的分辨率与深度或 IR 图像相同。 每个像素都是一个 8 位值。 可以调用 `k4abt_frame_get_body_index_map` 从 `k4abt_frame_t` 中查询它。 开发人员负责通过调用 `k4a_image_release()` 释放人体索引映射的内存。

## <a name="next-steps"></a>后续步骤

[生成第一个人体跟踪应用](build-first-body-app.md)
