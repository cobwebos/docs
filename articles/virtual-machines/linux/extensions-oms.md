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
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c2d14be5f27a775a14039bd63c5ccb5cd7b10f9a
ms.lasthandoff: 04/26/2017


---
# <a name="oms-virtual-machine-extension-for-linux"></a>适用于 Linux 的 OMS 虚拟机扩展

## <a name="overview"></a>概述

Operations Management Suite (OMS) 提供跨云和本地资产的监视、警报和警报修正功能。 适用于 Linux 的 OMS 代理虚拟机扩展由 Microsoft 发布和提供支持。 该扩展在 Azure 虚拟机上安装 OMS 代理，并将虚拟机注册到现有的 OMS 工作区中。 本文档详细介绍适用于 Linux 的 OMS 虚拟机扩展支持的平台、配置和部署选项。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

OMS 代理扩展可以针对这些 Linux 分发运行。

| 分发 | 版本 |
|---|---|
| CentOS Linux | 5、6 和 7 |
| Oracle Linux | 5、6 和 7 |
| Red Hat Enterprise Linux Server | 5、6 和 7 |
| Debian GNU/Linux | 6、7 和 8 |
| Ubuntu | 12.04 LTS、14.04 LTS、15.04、15.10 和 16.04 LTS |
| SUSE Linux Enterprise Server | 11 和 12 |

### <a name="internet-connectivity"></a>Internet 连接

适用于 Linux 的 OMS 代理扩展要求目标虚拟机已连接到 Internet。 

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示 OMS 代理扩展的架构。 此扩展需要目标 OMS 工作区的工作区 ID 和工作区密钥，这些值在 OMS 门户中提供。 由于工作区密钥应视为敏感数据，因此它应存储在受保护的设置配置。 Azure VM 扩展保护的设置数据已加密，并且只能在目标虚拟机上解密。 请注意，**workspaceId** 和 **workspaceKey** 区分大小写。

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.3",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>属性值

| 名称 | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| 发布者 | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.3 |
| workspaceId (e.g) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (e.g) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>模板部署

可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 部署需要部署后配置（例如，加入到 OMS）的一个或多个虚拟机时，模板是理想选择。 包含 OMS 代理 VM 扩展的示例 Resource Manager 模板可以在 [Azure 快速入门库](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)中找到。 

虚拟机扩展的 JSON 可以嵌套在虚拟机资源内，或放置在 Resource Manager JSON 模板的根级别或顶级别。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/resource-manager-template-child-resource.md)。 

以下示例假定 OMS 扩展嵌套在虚拟机资源内。 嵌套扩展资源时，JSON 放置在虚拟机的 `"resources": []` 对象中。

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.3",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

将扩展 JSON 放置在模板的根部时，资源名称包括对父虚拟机的引用，并且类型反映了嵌套配置。  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.3",
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
azure vm extension set myResourceGroup myVM \
  OmsAgentForLinux Microsoft.EnterpriseCloud.Monitoring 1.3 \
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

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>错误代码及其含义

| 错误代码 | 含义 | 可能的操作 |
| :---: | --- | --- |
| 2 | 提供给 shell 捆绑包的无效选项 | |
| 3 | 未向 shell 捆绑包提供选项 | |
| 4 | 无效的包类型 | |
| 5 | 必须将 shell 捆绑包作为根目录执行 | |
| 6 | 无效的包体系结构 | |
| 10 | VM 已连接至 OMS 工作区 | 要将 VM 连接到扩展架构中指定的工作区，请在公共设置中将“stopOnMultipleConnections”设置为 false，或删除该属性。 连接到工作区后，此 VM 立即开始计费。 |
| 11 | 提供给扩展的无效配置 | 按上述示例设置部署所需的所有属性值。 |
| 20 | SCX/OMI 安装失败 | |
| 21 | SCX/Provider 工具包安装失败 | |
| 22 | 捆版包安装失败 | |
| 23 | SCX 或 OMI 包已安装 | |
| 30 | 内部捆绑错误 | |
| 51 | VM 的操作系统不支持此扩展 | |
| 60 | 不支持的 OpenSSL 版本 | 安装满足[程序包要求](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#package-requirements)的 OpenSSL 版本。 |
| 61 | 缺少 Python ctypes 库 | 安装 Python ctypes 库或程序包 (python-ctypes)。 |
| 62 | 缺少 tar 程序 | 安装 tar。 |
| 63 | 缺少 sed 程序 | 安装 sed。 |

有关其他故障排除信息，可查看 [OMS-Agent-for-Linux 故障排除指南](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#)。

### <a name="support"></a>支持

如果你对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/en-us/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/en-us/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/en-us/support/faq/)。

