---
title: "在 Azure Cloud Shell（预览版）中持久保存文件 | Microsoft Docs"
description: "演练 Azure Cloud Shell 如何持久保存文件。"
services: azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: damaerte
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 933af3860bfe087a0b4db7eff53d4b978a1475da
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-works"></a>Cloud Shell 工作原理
Cloud Shell 通过以下方法持久保存文件： 
* 将指定的文件共享装载为 `$Home` 目录中的 `clouddrive` 以便直接进行文件共享交互。

## <a name="list-cloud-drive-file-shares"></a>列出云驱动器文件共享
`Get-CloudDrive` 命令检索云驱动器当前在 Cloud Shell 中装载的文件共享信息。 <br>
![运行 Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>卸载云驱动器
随时可以卸载已装载到 Cloud Shell 的文件共享。 如果删除了文件共享，则在下一个会话中，系统会提示创建并装载一个新的文件共享。

`Dismount-CloudDrive` 命令从当前存储帐户中卸载文件共享。 卸载云驱动器会终止当前会话。 在下一次会话期间，系统会提示用户创建并装载新的文件共享。
![运行 Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!include [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>后续步骤
[PowerShell 快速入门](quickstart-powershell.md) <br>
[了解 Azure 文件存储](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[了解存储标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
