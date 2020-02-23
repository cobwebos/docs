---
title: 重置 Azure Kinect DK
description: 介绍如何将 Azure Kinect DK 设备重置为出厂映像
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, 重置
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200633"
---
# <a name="reset-azure-kinect-dk"></a>重置 Azure Kinect DK

你可能遇到过需要将 Azure Kinect DK 重置为出厂映像的情况（例如，在未正确安装固件更新时）。

1. 关闭 Azure Kinect DK 的电源。 为此，请拔下 USB 数据线和电源线。
  ![显示遮盖重置按钮的螺丝位置的插图。](media/reset-azure-kinect-dk-diagram.png)
1. 若要找到重置按钮，请拆下三脚架安装锁孔中的螺丝。
1. 重新连接电源线。
1. 将拉直的回形针的尖端插入三脚架安装锁孔中的空螺丝孔。
1. 使用回形针轻按重置按钮并按住不放。
1. 在按住重置按钮的同时，重新连接 USB 数据线。
1. 大约 3 秒后，电源指示灯会变为琥珀色。 指示灯变色后，松开重置按钮。  
   
   松开重置按钮后，电源指示灯会以白色和琥珀色闪烁，此时设备正在重置。 
1. 等待电源指示灯变为稳定白色。
1. 在三脚架安装锁孔中装回螺丝，盖住重置按钮。
1. 使用 Azure Kinect 查看器验证固件是否已重置。 为此，请启动 [Azure Kinect 查看器](azure-kinect-viewer.md)，选择“设备固件版本信息”查看安装在 Azure Kinect DK 上的固件版本。 

请始终确保在设备上安装最新的固件。 若要获取最新的固件版本，请使用 Azure Kinect 固件工具。 有关如何检查固件状态的详细信息，请参阅[检查设备固件版本](azure-kinect-firmware-tool.md#check-device-firmware-version)。

## <a name="related-topics"></a>相关主题

- [关于 Azure Kinect DK](about-azure-kinect-dk.md)
- [设置 Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Azure Kinect DK 硬件规格：工作环境](hardware-specification.md#operating-environment)
- [Azure Kinect 固件工具](azure-kinect-firmware-tool.md)
- [Azure Kinect 查看器](azure-kinect-viewer.md)
- [跨多个 Azure Kinect DK 设备进行同步](multi-camera-sync.md)
