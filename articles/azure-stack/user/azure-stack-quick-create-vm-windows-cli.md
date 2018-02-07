---
title: "在使用 Azure CLI 的 Azure 堆栈上创建 Windows 虚拟机 |Microsoft 文档"
description: "了解如何在使用 Azure CLI 的 Azure 堆栈上创建 Windows VM"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: ea972db9ce3488d9a46a7d059714c8bbe820d47d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>在使用 Azure CLI 的 Azure 堆栈上创建 Windows 虚拟机

Azure CLI 用于创建和从命令行管理 Azure 堆栈资源。 此指南详细介绍如何使用 Azure CLI 在 Azure 堆栈中创建 Windows Server 2016 的虚拟机。 虚拟机创建后，将使用远程桌面，安装 IIS，进行连接，然后查看默认网站。 

## <a name="prerequisites"></a>先决条件 

* 请确保 Azure 堆栈运算符已添加到 Azure 堆栈应用商店的"Windows Server 2016"映像。  

* Azure 堆栈需要特定版本的 Azure CLI 来创建和管理资源。 如果你没有为 Azure 堆栈配置的 Azure CLI，请按照步骤[安装和配置 Azure CLI](azure-stack-connect-cli.md)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是到哪些 Azure 堆栈部署和管理资源的逻辑容器。 从开发工具包或 Azure 堆栈集成运行的系统[az 组创建](/cli/azure/group#az_group_create)命令来创建资源组。 我们已分配了本文档中的所有变量的值，你可以使用它们也将分配一个不同的值。 下面的示例创建一个名为 myResourceGroup 中的本地位置的资源组。

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建 VM。 以下示例创建名为 myVM 的 VM。 此示例使用 Demouser 管理用户名称和Demouser@123作为密码。 更新这些值，使其适用于环境。 时连接到虚拟机，需要这些值。

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

创建 VM 后，请记下*PublicIPAddress*输出，你将用于访问 VM 的参数。
 
## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

默认情况下，仅 RDP 允许连接到 Windows 虚拟机部署在 Azure 堆栈。 如果此 VM 会用作 Web 服务器，则需要从 Internet 打开端口 80。 使用 [az vm open-port](/cli/azure/vm#open-port) 命令打开所需端口。

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

使用以下命令创建与虚拟机的远程桌面会话。 将 IP 地址替换为你的虚拟机的公共 IP 地址。 出现提示时，输入创建虚拟机时使用的凭据。

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

## <a name="next-steps"></a>接下来的步骤

在本快速入门教程，你已部署简单的 Windows 虚拟机。 若要了解有关 Azure 堆栈的虚拟机的详细信息，继续到[Azure 堆栈中的虚拟机的注意事项](azure-stack-vm-considerations.md)。
