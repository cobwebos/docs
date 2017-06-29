---
title: "Azure 快速入门 - 创建 Windows VM CLI | Microsoft 文档"
description: "快速了解如何使用 Azure CLI 创建 Windows 虚拟机。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 01321cb74cce35fc01824d2c6c67211caab33258
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli"></a>使用 Azure CLI 创建 Windows 虚拟机

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本指南详细介绍如何使用 Azure CLI 部署运行 Windows Server 2016 的虚拟机。 部署完成后，我们将连接到服务器并安装 IIS。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#create) 创建 VM。 

以下示例创建一个名为 *myVM* 的 VM。 此示例使用 *azureuser* 作为管理用户名，使用 *myPassword12* 作为密码。 更新这些值，使其适用于你的环境。 创建与虚拟机的连接时，需要这些值。

```azurecli-interactive 
az vm create `
  --resource-group myResourceGroup `
  --name myVM --image win2016datacenter `
  --admin-username azureuser `
  --admin-password myPassword12
```

创建 VM 后，Azure CLI 将显示类似于以下示例的信息。 记下 `publicIpAaddress`。 此地址用于访问 VM。

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80 

默认情况下，仅允许通过 RDP 连接登录到 Azure 中部署的 Windows 虚拟机。 如果此 VM 将用作 Web 服务器，则需要从 Internet 打开端口 80。 使用 [az vm open-port](/cli/azure/vm#open-port) 命令打开所需端口。  
 
 ```azurecli-interactive  
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```


## <a name="connect-to-virtual-machine"></a>连接到虚拟机

使用以下命令创建与虚拟机的远程桌面会话。 将 IP 地址替换为你的虚拟机的公共 IP 地址。 出现提示时，输入创建虚拟机时使用的凭据。

```bash 
mstsc /v:<Public IP Address>
```

## <a name="install-iis-using-powershell"></a>使用 PowerShell 安装 IIS

现在，你已登录到 Azure VM，可以使用单行 PowerShell 安装 IIS，并启用本地防火墙规则以允许 Web 流量。 打开 PowerShell 提示符并运行以下命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>查看 IIS 欢迎页

IIS 已安装，并且现在已从 Internet 打开 VM 上的端口 80 - 你可以使用所选的 Web 浏览器查看默认的 IIS 欢迎页。 请务必使用前面记录的公共 IP 地址访问默认页面。 

![IIS 默认站点](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [az group delete](/cli/azure/group#delete) 命令将其删除。

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了一台简单的虚拟机、一条网络安全组规则，并安装了一台 Web 服务器。 若要详细了解 Azure 虚拟机，请继续学习 Windows VM 的教程。

> [!div class="nextstepaction"]
> [Azure Windows 虚拟机教程](./tutorial-manage-vm.md)

