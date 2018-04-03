---
title: 使用 Azure CLI 在 Azure Stack 中创建 Windows 虚拟机 | Microsoft Docs
description: 了解如何使用 Azure CLI 在 Azure Stack 中创建 Windows VM
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 2a4eb909c39051ce9fa2efd7e7997644d9b8b1b1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>使用 Azure CLI 在 Azure Stack 中创建 Windows 虚拟机

Azure CLI 用于从命令行创建和管理 Azure Stack 资源。 本指南详细介绍如何使用 Azure CLI 在 Azure Stack 中创建 Windows Server 2016 虚拟机。 创建虚拟机后，可以使用远程桌面建立连接、安装 IIS，然后查看默认网站。 

## <a name="prerequisites"></a>必备组件 

* 确保 Azure Stack 运营商已将“Windows Server 2016”映像添加到 Azure Stack Marketplace。  

* Azure Stack 需要使用特定版本的 Azure CLI 来创建和管理资源。 如果未针对 Azure Stack 配置 Azure CLI，请遵循[安装和配置 Azure CLI](azure-stack-version-profiles-azurecli2.md) 的步骤。

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure Stack 资源的逻辑容器。 在开发工具包或 Azure Stack 集成系统中，运行 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 我们已为本文档中的所有变量赋值，可以按原样使用这些值，或分配不同的值。 以下示例在本地位置创建名为 myResourceGroup 的资源组。

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建 VM。 以下示例创建名为 myVM 的 VM。 此示例使用 Demouser 作为管理用户名，使用 Demouser@123 作为密码。 更新这些值，使其适用于环境。 连接到虚拟机时需要使用这些值。

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

创建 VM 后，请记下输出的 *PublicIPAddress* 参数，稍后要使用它来访问 VM。
 
## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

默认情况下，仅允许通过 RDP 连接访问 Azure Stack 中部署的 Windows 虚拟机。 如果此 VM 将用作 Web 服务器，则需要从 Internet 打开端口 80。 使用 [az vm open-port](/cli/azure/vm#open-port) 命令打开所需端口。

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

使用以下命令创建与虚拟机的远程桌面会话。 将 IP 地址替换为虚拟机的公共 IP 地址。 出现提示时，输入创建虚拟机时使用的凭据。

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>使用 PowerShell 安装 IIS

登录到 Azure VM 后，可以使用单行 PowerShell 安装 IIS，并启用本地防火墙规则以允许 Web 流量。 打开 PowerShell 提示符并运行以下命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>查看 IIS 欢迎页

IIS 已安装，并且现在已从 Internet 打开 VM 上的端口 80 - 可以使用所选的 Web 浏览器查看默认的 IIS 欢迎页。 请务必使用前面记录的公共 IP 地址访问默认页面。 

![IIS 默认站点](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png) 

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令将其删除。

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们部署了一个简单的 Windows 虚拟机。 有关 Azure Stack 虚拟机的详细信息，请转到 [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)。
