---
title: 在 Azure 中排查 Windows VM 部署问题 | Microsoft 文档
description: 排查在 Azure 中创建新 Windows 虚拟机时遇到的 Resource Manager 部署问题
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: DavidCBerry13
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: daberry
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78db1ba0eaff0dce83ed13e9f20c3c5a5b96bf9c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120957"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>排查在 Azure 中新建 Windows VM 时遇到的部署问题
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>常见问题
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

若有其他 VM 部署问题和疑问，请参阅[排查 Azure 中的 Windows 虚拟机部署问题](troubleshoot-deploy-vm-windows.md)。

## <a name="collect-activity-logs"></a>收集活动日志
若要开始故障排除，请收集活动日志，以识别与问题相关的错误。 以下链接包含有关要遵循的过程的详细信息。

[查看部署操作](../../azure-resource-manager/templates/deployment-history.md)

[通过查看活动日志管理 Azure 资源](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y：** 如果 OS 是通用的 Windows，并且是使用通用设置上载和/或捕获的，则不会有任何错误。 同理，如果 OS 是通用的 Windows，并且是使用专用设置上传和/或捕获的，则不会有任何错误。

**上载错误：**

**N<sup>1</sup>：** 如果 OS 是通用的 Windows，但是以专用设置上载的，则会发生预配超时错误，并且 VM 会卡在 OOBE 屏幕上。

**N<sup>2</sup>：** 如果 OS 是专用的 Windows，但是以通用设置上传的，则会发生预配失败错误，并且 VM 会卡在 OOBE 屏幕上，因为新 VM 是以原始计算机名称、用户名和密码运行的。

**解决方法**

若要解决这两个错误，请[使用 Add-AzVhd 上传原始 VHD](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd)、可用的本地设置、以及与该 OS（通用/专用）相同的设置。 若要以通用设置上传，请记得先运行 sysprep。

**捕获错误：**

**N<sup>3</sup>：** 如果 OS 是通用的 Windows，但是以专用设置捕获的，则会发生预配超时错误，因为标记为通用的原始 VM 不可用。

**N<sup>4</sup>：** 如果 OS 是专用的 Windows，但是以专用设置捕获的，则会发生预配失败错误，因为新 VM 是以原始计算机名称、用户名和密码运行的。 此外，标记为专用的原始 VM 不可用。

**解决方法**

若要解决这两个错误，请从门户中删除当前映像，并[从当前 VHD 重新捕获映像](../windows/create-vm-specialized.md)，该映像具有与该 OS（通用/专用）相同的设置。

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>问题：自定义/库/应用商店映像；分配失败
当新的 VM 请求被固定到不支持所请求的 VM 大小、或没有可用空间可处理请求的群集时，便会发生此错误。

**原因 1：** 群集不支持请求的 VM 大小。

**解决方法 1：**

* 以更小的 VM 大小重试请求。
* 如果无法更改请求的 VM 大小：
  * 停止可用性集中的所有 VM。
    单击“资源组”  资源组 >  *“资源”*  > 可用性集  “虚拟机” > *虚拟机* > “停止”。   >    >  
  * 所有 VM 都停止后，创建所需大小的新 VM。
  * 先启动新 VM，选择每个已停止的 VM，并单击“启动”。 

**原因 2：** 群集没有可用的资源。

**解决方法 2：**

* 稍后重试请求。
* 如果新 VM 属于不同的可用性集
  * 在不同的可用性集（位于同一区域）中创建新 VM。
  * 将新 VM 添加到同一虚拟网络。

## <a name="next-steps"></a>后续步骤
如果在 Azure 中启动已停止的 Windows VM 或调整现有 Windows VM 的大小时遇到问题，请参阅[排查在 Azure 中重新启动现有 Windows 虚拟机或调整其大小时遇到的 Resource Manager 部署问题](restart-resize-error-troubleshooting.md)。


