---
title: 连接到 Windows Server VM | Microsoft Docs
description: 了解如何使用 Azure 门户和 Resource Manager 部署模型连接并登录到 Windows VM。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 1980ee0bdea1316f6117d9deb7d2adb9df941768
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162615"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>如何连接并登录到运行 Windows 的 Azure 虚拟机
可以从 Windows 桌面使用 Azure 门户中的“连接”按钮来启动远程桌面 (RDP) 会话。 首先连接到虚拟机，然后登录。

若要从 Mac 连接到 Windows VM，需要为 Mac 安装 RDP 客户端，例如 [Microsoft 远程桌面](https://aka.ms/rdmac)。

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机
1. 如果尚未登录 [Azure 门户](https://portal.azure.com/)，请先登录。
2. 在左侧菜单中，选择“虚拟机”。
3. 从列表中选择虚拟机。
4. 在虚拟机页顶部，选择“连接”。
2. 在“连接到虚拟机”页上，选择相应的 IP 地址和端口。 在大多数情况下，应使用默认的 IP 地址和端口。 选择“下载 RDP 文件”。 如果 VM 具有实时策略集，则首先需要选择“请求访问”按钮来请求访问，然后才能下载 RDP 文件。 有关实时策略的详细信息，请参阅[使用实时策略管理虚拟机访问](../../security-center/security-center-just-in-time.md)。
2. 打开下载的 RDP 文件，然后在出现提示时选择“连接”。 
2. 将收到 `.rdp` 文件来自未知发布服务器的警告。 这是正常情况。 在“远程桌面连接”窗口中，选择“连接”以继续。
   
    ![有关未知发布者的警告的屏幕截图。](./media/connect-logon/rdp-warn.png)
3. 在“Windows 安全性”窗口中，依次选择“更多选择”、“使用其他帐户”。 在虚拟机上输入帐户的凭据，然后选择“确定”。
   
     **本地帐户**：通常，这是创建虚拟机时指定的本地帐户用户名和密码。 在本示例中，域是虚拟机的名称，输入格式为 *vmname*&#92;*username*。  
   
    **已加入域的 VM**：如果 VM 属于某个域，请以*域*&#92;*用户名*格式输入用户名。 该帐户还需要属于管理员组或已被授予 VM 的远程访问权限。
   
    **域控制器**：如果 VM 是域控制器，请输入该域的域管理员帐户的用户名和密码。
4. 选择“是”验证虚拟机的标识并完成登录。
   
   ![显示有关验证 VM 标识的消息的屏幕截图。](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > 如果门户中的“连接”按钮灰显，并且你未通过 [Express Route](../../expressroute/expressroute-introduction.md) 或[站点到站点 VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 连接来连接到 Azure，则必须先为 VM 创建并分配一个公共 IP 地址，然后才能使用 RDP。 有关详细信息，请参阅 [Azure 中的公共 IP 地址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)。
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>使用 PowerShell 连接到虚拟机

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

如果使用 PowerShell 并安装了 Azure PowerShell 模块，则还可以使用`Get-AzRemoteDesktopFile` cmdlet 进行连接，如下所示。

此示例将立即启动 RDP 连接，引导完成与上述类似的提示。

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

还可以保存 RDP 文件以供将来使用。

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>后续步骤
如果连接有困难，请参阅[远程桌面连接故障排除](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

