---
title: 在 Azure 虚拟网络中 Cloud Shell
description: 将 Cloud Shell 部署到 Azure 虚拟网络
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: 722d935c242a51ddfc01377676f026b71a8951b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89468532"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>将 Cloud Shell 部署到 Azure 虚拟网络
> [!NOTE]
> 此功能处于公共预览版中。

常规 Cloud Shell 会话在独立于资源的 Microsoft 网络中的容器中运行。 这意味着，在容器内运行的命令无法访问只能从特定虚拟网络访问的资源。 例如，不能使用 SSH 从 Cloud Shell 连接到只有专用 IP 地址的虚拟机，或者使用 kubectl 连接到已锁定访问权限的 Kubernetes 群集。 

此可选功能可解决这些限制，并允许你将 Cloud Shell 部署到你控制的 Azure 虚拟网络。 容器可以从此处与所选虚拟网络中的资源进行交互。  

在此方案中，你可以看到将部署和使用的资源体系结构。

![阐释 Cloud Shell 隔离 VNET 体系结构。](media/private-vnet/data-diagram.png)

你需要创建多个资源来支持此功能，然后才能在你自己的 Azure 虚拟网络中使用 Cloud Shell。 本文介绍如何使用 ARM 模板设置所需的资源。

> [!NOTE]
> 只需为虚拟网络设置一次这些资源。 然后，可以将这些用户共享给有权访问虚拟网络的所有管理员。

## <a name="required-network-resources"></a>所需的网络资源

### <a name="virtual-network"></a>虚拟网络
虚拟网络定义在其中创建一个或多个子网的地址空间。

需要确定要用于 Cloud Shell 的所需虚拟网络。 这通常是一个现有的虚拟网络，其中包含你想要管理的资源，或包含与包含资源的网络的对等网络。

### <a name="subnet"></a>子网
在选定的虚拟网络中，专用子网必须用于 Cloud Shell 容器。 此子网将委托给 (ACI) 服务的 Azure 容器实例。  当用户请求虚拟网络中的 Cloud Shell 容器时，Cloud Shell 使用 ACI 来创建位于此委托子网中的容器。  此子网中不能创建其他资源。

### <a name="network-profile"></a>网络配置文件
网络配置文件是用于指定资源的某些网络属性的 Azure 资源的网络配置模板。

### <a name="azure-relay"></a>Azure 中继
[Azure 中继](../azure-relay/relay-what-is-it.md)允许无法直接访问的两个终结点进行通信。 在这种情况下，它用于允许管理员浏览器与专用网络中的容器进行通信。

用于 Cloud Shell 的 Azure 中继实例可以配置为控制哪些网络可以访问容器资源： 
- 可从公共 internet 进行访问：在此配置中，Cloud Shell 提供了一种方法，用于从外部访问内部资源。 
- 可从指定的网络进行访问：在此配置中，管理员将必须从在适当网络中运行的计算机访问 Azure 门户，才能使用 Cloud Shell。

## <a name="storage-requirements"></a>存储要求
与标准 Cloud Shell 一样，使用虚拟网络中的 Cloud Shell 时需要存储帐户。 每个管理员都需要一个文件共享来存储其文件。  需要从 Cloud Shell 使用的虚拟网络中访问存储帐户。 

## <a name="virtual-network-deployment-limitations"></a>虚拟网络部署限制
* 由于涉及到额外的网络资源，在虚拟网络中开始 Cloud Shell 通常比标准 Cloud Shell 会话要慢。

* 在预览期间，虚拟网络中的 Cloud Shell 支持的区域更少。 当前限制为： WestUS 和 WestCentralUS。

* [Azure 中继](../azure-relay/relay-what-is-it.md) 不是免费服务，请查看其 [定价](https://azure.microsoft.com/pricing/details/service-bus/)。 在 Cloud Shell 方案中，每个管理员使用 Cloud Shell 时，会使用一个混合连接。 Cloud Shell 会话完成后，连接将自动关闭。

## <a name="register-the-resource-provider"></a>注册资源提供程序

需要在保存要使用的虚拟网络的订阅中注册 ContainerInstances 资源提供程序。 选择相应的订阅 `Set-AzContext -Subscription {subscriptionName}` ，然后运行：

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

如果 **RegistrationState** 为 `Registered` ，则无需执行任何操作。 如果已 `NotRegistered` 运行，则运行 `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` 。 

## <a name="deploy-network-resources"></a>部署网络资源
 
### <a name="create-a-resource-group-and-virtual-network"></a>创建资源组和虚拟网络
如果你已有希望连接到的所需 VNET，请跳过此部分。

在 Azure 门户中，或使用 Azure CLI、Azure PowerShell 等，在新资源组中创建资源组和虚拟网络， **资源组和虚拟网络必须位于同一区域**。

> [!NOTE]
> 在公共预览版中，资源组和虚拟网络必须位于 "WestCentralUS" 或 "WestUS"。

### <a name="arm-templates"></a>ARM 模板
使用 [Azure 快速入门模板](https://aka.ms/cloudshell/docs/vnet/template) 在虚拟网络中创建 Cloud Shell 资源，并使用 [Azure 快速入门模板](https://aka.ms/cloudshell/docs/vnet/template/storage) 创建必要的存储。 记下资源名称，主要是文件共享名称。

### <a name="open-relay-firewall"></a>打开中继防火墙
导航到使用上述模板创建的中继，在 "设置" 中选择 "网络"，允许从浏览器网络访问中继。 默认情况下，中继只能从在其中创建它的虚拟网络进行访问。 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>将 Cloud Shell 配置为使用虚拟网络。
> [!NOTE]
> 必须完成此步骤，每个管理员都将使用 Cloud Shell。

部署完成上述步骤后，请导航到 Azure 门户或中的 Cloud Shell https://shell.azure.com 。 每次需要连接到隔离的 Cloud Shell 体验时，都必须使用这些体验之一。

> [!NOTE]
> 如果过去已使用 Cloud Shell，则必须卸载现有 clouddrive。 若要 `clouddrive unmount` 从活动 Cloud Shell 会话运行此操作，请刷新页面。

连接到 Cloud Shell，系统将提示您首次运行体验。 选择首选 shell 体验，选择 "显示高级设置"，并选择 "显示 VNET 隔离设置" 框。 在弹出窗口中填写字段。  大多数字段将自动填充到可与虚拟网络中的 Cloud Shell 关联的可用资源。  用户需要填写文件共享名。


![说明 Cloud Shell 隔离 VNET 首次体验设置。](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>后续步骤
[了解 Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)