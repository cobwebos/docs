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
ms.date: 04/23/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 381c1c37b0675d97adc058979a5d9b5c4fd2cc8b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155150"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>快速入门： Azure 堆栈中使用 Azure CLI 创建 Windows Server 虚拟机

‎*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

你可以通过 Azure CLI 创建 Windows Server 2016 的虚拟机。 按照这篇文章创建和使用虚拟机中的步骤。 这篇文章还为你提供了以下步骤：

* 连接到远程客户端的虚拟机。
* 安装 IIS web 服务器并查看默认的主页。
* 清理你的资源。

## <a name="prerequisites"></a>必备组件

* 请确保 Azure 堆栈运算符添加**Windows Server 2016**向 Azure 堆栈市场映像。

* Azure Stack 需要使用特定版本的 Azure CLI 来创建和管理资源。 如果未针对 Azure Stack 配置 Azure CLI，请遵循[安装和配置 Azure CLI](azure-stack-version-profiles-azurecli2.md) 的步骤。

## <a name="create-a-resource-group"></a>创建资源组

资源组是你可以在其中部署和管理 Azure 堆栈资源的逻辑容器。 从 Azure 堆栈环境，运行[az 组创建](/cli/azure/group#az_group_create)命令来创建资源组。

>[!NOTE]
 下面的代码示例中的所有变量分配值。 但是，如果你希望可以将分配新值。

以下示例在本地位置创建名为 myResourceGroup 的资源组。

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

通过创建虚拟机 (VM) [az vm 创建](/cli/azure/vm#az_vm_create)命令。 以下示例创建名为 myVM 的 VM。 此示例使用 Demouser 管理用户名称和Demouser@123作为用户密码。 将这些值更改为可适合于你的环境。

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

创建 VM 后， **PublicIPAddress**在输出中的参数将包含虚拟机的公共 IP 地址。 记下此地址，因为你需要使用它来访问虚拟机。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

因为此 VM 要运行的 IIS web 服务器，你需要打开端口 80 对 Internet 流量。

使用[az vm 打开端口](/cli/azure/vm#open-port)命令来打开端口 80。

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

使用下一个命令创建与你的虚拟机的远程桌面连接。 将"公共 IP 地址"替换为你的虚拟机的 IP 地址。 出现提示时，输入用户名和密码，用于虚拟机。

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>使用 PowerShell 安装 IIS

现在，你已登录到虚拟机，你可以使用 PowerShell 安装 IIS。 在虚拟机上启动 PowerShell 并运行以下命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>查看 IIS 欢迎页

你选择的 web 浏览器可用于查看默认的 IIS 欢迎页。 使用上一节中所述的公共 IP 地址访问默认页面。

![IIS 默认站点](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>清理资源

清理不不再需要的资源。 使用[az 组删除](/cli/azure/group#az_group_delete)命令删除资源组，虚拟机，以及所有相关资源。

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门教程，你将部署基本的 Windows Server 虚拟机。 有关 Azure Stack 虚拟机的详细信息，请转到 [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)。
