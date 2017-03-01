---
title: "使用 Linux VM 扩展创作模板 | Microsoft Docs"
description: "了解如何使用 Linux VM 扩展创作 Azure Resource Manager 模板"
services: virtual-machines-linux
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 322f8f0b-6697-4acb-b5f3-b3f58d28358b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 42c2a12551db35f4cb3994179ba2e277145f2d9b


---
# <a name="authoring-azure-resource-manager-templates-with-linux-vm-extensions"></a>使用 Linux VM 扩展创作 Azure Resource Manager 模板
[!INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

在 Azure CLI 中运行以下命令：

      Azure VM extension list

此命令返回发布者名称、扩展名称和版本，如下所示：

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

若要查看 Linux 扩展的示例配置，请单击文档 [Linux 扩展示例](virtual-machines-linux-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

请参阅以下内容，获取完整的 VM 扩展模板。

[Linux VM 上的自定义脚本扩展](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

创作模板之后，可以使用 Azure CLI 部署模板。




<!--HONumber=Nov16_HO3-->


