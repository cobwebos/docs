---
title: "适用于 Linux 的 OMS Azure 虚拟机扩展 | Microsoft Docs"
description: "使用虚拟机扩展在 Linux 虚拟机上部署 OMS 代理。"
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 9f27890e52cb7a9a0d46f2bb84bfe92f7c6fff37
ms.openlocfilehash: 9864314956d79317785c1c2a4bc87621bc6a3e2d


---
# <a name="oms-virtual-machine-extension-for-linux"></a>适用于 Linux 的 OMS 虚拟机扩展

## <a name="overview"></a>概述

Operations Management Suite (OMS) 提供跨云和本地资产的监视、警报和警报修正功能。 适用于 Linux 的 OMS 代理虚拟机扩展由 Microsoft 发布和提供支持。 该扩展在 Azure 虚拟机上安装 OMS 代理，并将虚拟机注册到现有的 OMS 工作区中。 本文档详细介绍适用于 Linux 的 OMS 虚拟机扩展支持的平台、配置和部署选项。

有关 Azure 虚拟机扩展的常规信息，请参阅[虚拟机扩展概述](./virtual-machines-linux-extensions-features.md)。

有关 Operations Management Suite 的详细信息，请参阅 [Operations Management Suite 概述](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite)。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

OMS 代理扩展可以针对这些 Linux 分发运行。

| 分发 | 版本 |
|---|---|
| CentOS Linux | 5、6 和 7 |
| Oracle Linux | 5、6 和 7 |
| Red Hat Enterprise Linux Server | 5、6 和 7 |
| Debian GNU/Linux | 6、7 和 8 |
| Ubuntu | 12.04 LTS、14.04 LTS、15.04 |
| SUSE Linux Enterprise Server | 11 和 12 |

### <a name="connectivity"></a>连接

适用于 Linux 的 OMS 代理扩展要求目标虚拟机已连接到 Internet。 

## <a name="extension-configuration"></a>扩展配置

适用于 Linux 的 OMS 代理虚拟机扩展需要目标 OMS 工作区的工作区 ID 和工作区密钥。 由于工作区密钥应视为敏感数据，因此将它存储在受保护的配置中。 Azure VM 扩展保护的配置已加密，并且只能在目标虚拟机上解密。 公共和专用配置在部署时指定，将在本文档的后续部分中详细介绍。

### <a name="public-configuration"></a>公共配置

公共配置的架构：

- workspaceId：（必需，字符串）要将虚拟机加入到其中的 OMS 工作区的 ID。

```json
{
  "workspaceId": "myWorkspaceId"
}
```

### <a name="private-configuration"></a>专用配置

公共配置的架构：

- workspaceKey：（必需，字符串）工作区的主/辅助共享密钥。

```json
{
  "workspaceKey": "myWorkSpaceKey"
}
```

## <a name="template-deployment"></a>模板部署

可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 部署需要部署后配置（例如，加入到 OMS）的一个或多个虚拟机时，模板是理想选择。 包含 OMS 代理 VM 扩展的示例 Resource Manager 模板可以在 [Azure 快速入门库](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)中找到。 

可以使用此按钮从本文档部署该示例：

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-oms-extension-ubuntu-vm%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

用于部署 OMS 代理 VM 扩展的 JSON 类似于以下 JSON 示例：

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<extension-deployment-name>",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

可以使用 Azure CLI 将 OMS 代理 VM 扩展部署到现有的虚拟机。 在部署 OMS 代理扩展之前，需创建 public.json 和 protected.json 文件。 在本文档的前面部分详细介绍了这些文件的架构。

```azurecli
azure vm extension set <resource-group> <vm-name> \
  OmsAgentForLinux Microsoft.EnterpriseCloud.Monitoring 1.0 \
  --public-config-path public.json  \
  --private-config-path protected.json
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure CLI 运行以下命令。

```azurecli
azure vm extension get myResourceGroup myVM
```

扩展执行输出将记录到以下文件：

`
/opt/microsoft/omsagent/bin/stdout
`

### <a name="support"></a>支持

如果你对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/en-us/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/en-us/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/en-us/support/faq/)。



<!--HONumber=Dec16_HO1-->


