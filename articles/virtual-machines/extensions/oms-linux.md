---
title: 适用于 Linux 的 azure 监视虚拟机扩展 |Microsoft Docs
description: 使用虚拟机扩展在 Linux 虚拟机上部署 Log Analytics 代理。
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/06/2019
ms.author: roiyz
ms.openlocfilehash: 8b24af016349db0fcfb4106a1e69da395e3d0150
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755155"
---
# <a name="azure-monitor-virtual-machine-extension-for-linux"></a>适用于 Linux 的 Azure Monitor 虚拟机扩展

## <a name="overview"></a>概述

Azure Monitor 日志提供跨云和本地资产的监视、警报和警报修正功能。 适用于 Linux 的 Log Analytics 代理虚拟机扩展由 Microsoft 发布和提供支持。 该扩展在 Azure 虚拟机上安装 Log Analytics 代理，并将虚拟机注册到现有的 Log Analytics 工作区中。 本文档详细介绍适用于 Linux 的 Azure Monitor 虚拟机扩展支持的平台、配置和部署选项。

>[!NOTE]
>从 Microsoft Operations Management Suite (OMS) 过渡到 Azure Monitor 期间，Windows 或 Linux 的 OMS 代理称为 Windows 或 Linux 的 Log Analytics 代理。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>必备组件

### <a name="operating-system"></a>操作系统

Log Analytics 代理扩展可以针对这些 Linux 发行版运行。

| 分发 | Version |
|---|---|
| CentOS Linux | 6 (x86/x64) 和 7 (x64) |
| Amazon Linux | 2017.09 (x64) | 
| Oracle Linux | 6 和 7 (x86/x64) |
| Red Hat Enterprise Linux Server | 6 (x86/x64) 和 7 (x64) |
| Debian GNU/Linux | 8 和 9 (x86/x64) |
| Ubuntu | 14.04 LTS (x86/x64)、16.04 LTS (x86/x64) 和 18.04 LTS (x64) |
| SUSE Linux Enterprise Server | 12 (x64) |

>[!NOTE]
>任何平台都不支持低于版本 1.x 的 OpenSSL，并且仅在 x86_64 平台（64 位）上支持版本 1.10。  
>

### <a name="agent-prerequisites"></a>代理先决条件

下表列出了在安装代理的受支持 Linux 发行版的所需的包。

|所需的包 |描述 |最低版本 |
|-----------------|------------|----------------|
|Glibc |    GNU C 库 | 2.5-12 
|Openssl    | OpenSSL 库 | 1.0.x 或 1.1.x |
|Curl | cURL Web 客户端 | 7.15.5 |
|Python-ctype | | 
|PAM | 可插入验证模块 | | 

>[!NOTE]
>收集 Syslog 消息时需要 rsyslog 或 syslog ng。 不支持将 Red Hat Enterprise Linux 版本 5、CentOS 和 Oracle Linux 版本 (sysklog) 上的默认 syslog 守护程序用于 syslog 事件收集。 要从这些发行版的此版本中收集 syslog 数据，应安装并配置 rsyslog 守护程序以替换 sysklog。

### <a name="agent-and-vm-extension-version"></a>代理和 VM 扩展版本
下表提供每次发布的 Azure Monitor VM 扩展和 Log Analytics 代理捆绑包的版本映射。 并附有 Log Analytics 代理捆绑包版本的发行说明链接。 发行说明包括有关可用于给定代理版本的 bug 修补程序和新功能的详细信息。  

| Azure Monitor Linux VM 扩展版本 | Log Analytics 代理捆绑包版本 | 
|--------------------------------|--------------------------|
|1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure 安全中心

Azure 安全中心自动预配 Log Analytics 代理并将其连接到 Azure 订阅中由 ASC 创建的默认 Log Analytics 工作区。 如果使用 Azure 安全中心，请勿按照本文档中的步骤运行。 这样做会覆盖已配置的工作区并断开与 Azure 安全中心的连接。

### <a name="internet-connectivity"></a>Internet 连接

适用于 Linux 的 Log Analytics 代理扩展要求目标虚拟机已连接到 Internet。 

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示 Log Analytics 代理扩展的架构。 此扩展需要目标 Log Analytics 工作区的工作区 ID 和工作区密钥，这些值可在 Azure 门户中的 [Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) 工作区中找到。 由于工作区密钥应视为敏感数据，因此它应存储在受保护的设置配置。 Azure VM 扩展保护的设置数据已加密，并且只能在目标虚拟机上解密。 请注意，**workspaceId** 和 **workspaceKey** 区分大小写。

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>上面的架构假定，将其放置在模板的根级别。 如果将其放在模板的虚拟机资源中，则应更改 `type` 和 `name` 属性，如[后文](#template-deployment)所述。
>

### <a name="property-values"></a>属性值

| 名称 | 值/示例 |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| workspaceId (e.g) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (e.g) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 部署需要部署后配置（例如，载入 Azure Monitor 日志）的一个或多个虚拟机时，模板是理想选择。 包含 Log Analytics 代理 VM 扩展的示例资源管理器模板可以在 [Azure 快速入门库](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)中找到。 

虚拟机扩展的 JSON 配置可以嵌套在虚拟机资源内，或放置在资源管理器 JSON 模板的根级别或顶级别。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources)。 

以下示例假定 VM 扩展嵌套在虚拟机资源内。 嵌套扩展资源时，JSON 放置在虚拟机的 `"resources": []` 对象中。

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

将扩展 JSON 放置在模板的根部时，资源名称包括对父虚拟机的引用，并且类型反映了嵌套的配置。  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
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

可以使用 Azure CLI 将 Log Analytics 代理 VM 扩展部署到现有的虚拟机。 将 workspaceId 和 workspaceKey 替换为 Log Analytics 工作区中的对应项   。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.7 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure CLI 运行以下命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

扩展执行输出将记录到以下文件：

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>错误代码及其含义

| 错误代码 | 含义 | 可能的操作 |
| :---: | --- | --- |
| 9 | 过早调用 enable | 将 [Azure Linux 代理](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent)更新为可用的最新版本。 |
| 10 | VM 已连接至 Log Analytics 工作区 | 要将 VM 连接到扩展架构中指定的工作区，请在公共设置中将“stopOnMultipleConnections”设置为 false，或删除该属性。 连接到工作区后，此 VM 立即开始计费。 |
| 11 | 提供给扩展的无效配置 | 按上述示例设置部署所需的所有属性值。 |
| 17 | Log Analytics 包安装失败 | 
| 19 | OMI 包安装失败 | 
| 20 | SCX 包安装失败 |
| 51 | VM 的操作系统不支持此扩展 | |
| 55 | 无法连接到 Azure Monitor 服务或缺少所需的包或 dpkg 包管理器已锁定| 确保系统具有 Internet 访问权限，或已提供有效 HTTP 代理。 此外，检查工作区 ID 的正确性，并验证是否已安装 curl 和 tar 实用程序。 |

有关其他故障排除信息，可查看 [Log Analytics-Agent-for-Linux 故障排除指南](../../azure-monitor/platform/vmext-troubleshoot.md)。

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
