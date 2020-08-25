---
title: Azure Stack Edge & Azure Data Box Gateway 2007 发行说明 |Microsoft Docs
description: 描述 Azure Stack 边缘的关键打开问题和解决方法以及运行2007版本 Data Box Gateway。
services: databox
author: twooley
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 07/14/2020
ms.author: twooley
ms.openlocfilehash: f9a08cda1466dbe284a0ac3c0e34b109be236883
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783920"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Azure Stack Edge 和 Azure Data Box Gateway 2007 发行说明

以下发行说明针对 Azure Stack Edge 和 Data Box Gateway 确定2007版本的重大打开问题和已解决问题。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署 Azure Stack Edge/Data Box Gateway 之前，请仔细查看发行说明中包含的信息。

此版本对应于软件版本：

- **Azure Stack Edge 2007 (1.6.1280.1667) ** -KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667) ** -KB 4566550

> [!NOTE]
> 更新2007只能应用于) 软件或更高版本的 (GA 版本的所有正在运行的设备。

## <a name="whats-new"></a>新增功能

此版本包含以下 bug 修复：

- **上传问题** -此版本修复了上传由于失败而重新启动的问题，这可能会降低上载完成的速度。 如果上传的数据集主要包含大小较大（相对于可用带宽）的文件，尤其是在带宽限制处于活动状态时，则会出现此问题。 此更改可确保在重新启动给定文件的上传之前，为上载完成提供充足的机会。

此版本还包含以下更新：

- 已更新 Windows VHD 的基本映像。
- 所有累积 Windows 更新和 .NET framework 更新均包括在2020年5月发布的时间。
- 此版本支持 Azure Stack 边缘设备上的 IoT Edge 1.0.9.3。

## <a name="known-issues-in-this-release"></a>本版本中的已知问题

对于此版本，不会记录任何新问题。 所有已记录版本的问题已从以前的版本中继续执行。 若要查看已知问题的列表，请参阅 [GA 版本中的已知问题](data-box-gateway-release-notes.md#known-issues-in-ga-release)。

## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Stack Edge](data-box-edge-deploy-prep.md)
- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
