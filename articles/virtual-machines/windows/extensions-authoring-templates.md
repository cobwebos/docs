---
title: "使用 Windows VM 扩展创作模板 | Microsoft Docs"
description: "了解如何使用 Windows VM 扩展创作 Azure Resource Manager 模板"
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 418dd1f7-ded8-45ab-9a5a-a59d245e2555
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 338668df33783d8ef62c6710f4d96ce805296fe5
ms.lasthandoff: 03/31/2017


---
# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>使用 Windows VM 扩展创作 Azure Resource Manager 模板
[!INCLUDE [virtual-machines-common-extensions-authoring-templates](../../../includes/virtual-machines-common-extensions-authoring-templates.md)]

从 Azure PowerShell，运行以下 Azure Powershell cmdlet：

      Get-AzureVMAvailableExtension


此 cmdlet 会返回发布者名称、扩展名称和版本，如下所示：

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

这三个属性分别映射到上述模板代码片段中的“publisher”、“type”和“typeHandlerVersion”。

> [!NOTE]
> 始终建议使用最新的扩展版本以获取最新功能。
> 
> 

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>确定扩展配置参数的架构
创作扩展模板的下一个步骤是确定提供配置参数的格式。 每个扩展均支持其自己的参数集。

若要查看 Windows 扩展的示例配置，请参阅 [Windows 扩展示例](extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

请参阅以下内容，获取完整的 VM 扩展模板。

[Windows VM 上的自定义脚本扩展](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

创作模板之后，可以使用 Azure PowerShell 部署模板。


