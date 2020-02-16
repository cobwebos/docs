---
title: 为 Azure 点到站点连接配置 Azure AD 身份验证 | Microsoft Docs
description: 本教程介绍如何为用户 VPN 配置 Azure Active Directory 身份验证。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: alzam
ms.openlocfilehash: b3508c4c8da5b4987fb5f38cf3bf701f2dda1097
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122028"
---
# <a name="tutorial-create-a-user-vpn-connection-by-using-azure-virtual-wan"></a>教程：使用 Azure 虚拟 WAN 创建用户 VPN 连接

本教程介绍如何为虚拟 WAN 中的用户 VPN 配置 Azure AD 身份验证，以通过 OpenVPN VPN 连接与 Azure 中的资源建立连接。 Azure Active Directory 身份验证仅适用于使用 OpenVPN 协议的网关以及运行 Windows 的客户端。

此类连接要求在客户端计算机上配置一个客户端。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 WAN
> * 创建中心
> * 创建 P2S 配置
> * 下载 VPN 客户端配置文件
> * 将 P2S 配置应用到中心
> * 将 VNet 连接到中心
> * 下载并应用 VPN 客户端配置
> * 查看虚拟 WAN
> * 查看资源运行状况

![虚拟 WAN 示意图](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>开始之前

在开始配置之前，请验证你是否符合以下条件：

* 你拥有一个要连接到的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../virtual-network/quick-create-portal.md)。

* 虚拟网络不包含任何虚拟网络网关。 如果虚拟网络包含网关（VPN 或 ExpressRoute），则必须删除所有网关。 此配置要求将虚拟网络改为连接到虚拟 WAN 中心网关。

* 获取中心区域的 IP 地址范围。 该中心是虚拟 WAN 创建和使用的虚拟网络。 为中心指定的地址范围不能与要连接到的任何现有虚拟网络重叠。 此外，它也不能与本地连接到的地址范围重叠。 如果不熟悉本地网络配置中的 IP 地址范围，则咨询能够提供此类详细信息的人员。

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="wan"></a>创建虚拟 WAN

从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

1. 导航到“虚拟 WAN”页。 在门户中，单击“+创建资源”  。 在搜索框中键入“虚拟 WAN”  ，然后选择 Enter。
2. 从结果中选择“虚拟 WAN”  。 在“虚拟 WAN”页上，单击“创建”以打开“创建 WAN”页  。
3. 在“创建 WAN”页的“基本信息”选项卡上，填写以下字段   ：

   ![虚拟 WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **订阅** - 选择要使用的订阅。
   * **资源组** - 新建资源组或使用现有的资源组。
   * **资源组位置** - 从下拉列表中选择资源位置。 WAN 是一个全局资源，不会驻留在某个特定区域。 但是，必须选择一个区域才能更轻松地管理和查找所创建的 WAN 资源。
   * **名称** - 键入要用于称呼 WAN 的名称。
   * **类型：** 标准。 如果创建基本 WAN，则只能创建基本中心。 基本中心仅支持 VPN 站点到站点连接。
4. 填写完字段后，单击“审阅 + 创建”  。
5. 验证通过后，选择“创建”以创建虚拟 WAN  。

## <a name="site"></a>创建空虚拟中心

1. 在虚拟 WAN 下选择“中心”，然后单击“+新建中心”。 

   ![新建站点](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. 在“创建虚拟中心”页上，请填写以下字段。

   **区域** - 选择要在其中部署虚拟中心的区域。

   **名称** - 输入要用于称呼虚拟中心的名称。

   **中心专用地址空间** - 用 CIDR 表示法来表示的中心地址范围。

   ![新建站点](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. 单击“查看 + 创建”  。
4. 在“验证已通过”页上，单击“创建”。  

## <a name="site"></a>创建新的 P2S 配置

P2S 配置定义连接远程客户端的参数。

1. 在虚拟 WAN 下，选择“用户 VPN 配置”  。

   ![新建配置](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. 单击“+创建用户 VPN 配置”  。

   ![新建配置](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. 输入信息，然后单击“创建” 

   ![新建配置](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="hub"></a>编辑中心分配

1. 导航到虚拟 WAN 下的“中心”边栏选项卡。 
2. 选择要将 VPN 服务器配置关联到的中心，然后单击省略号图标 (...)。

   ![新建站点](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. 单击“编辑虚拟中心”  。
4. 选中“包括点到站点网关”  复选框，然后选择所需的网关缩放单元  。

   ![新建站点](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. 输入用于为 VPN 客户端分配 IP 地址的“地址池”  。
6. 单击“确认”  。
7. 完成此操作最多需要 30 分钟。

## <a name="device"></a>下载 VPN 配置文件

使用 VPN 配置文件来配置客户端。

1. 在虚拟 WAN 的页面上，单击“用户 VPN 配置”  。
2. 在页面顶部，单击“下载用户 VPN 配置”  。
3. 完成创建文件后，可以单击相应的链接下载该文件。
4. 使用此配置文件配置 VPN 客户端。

## <a name="configure-user-vpn-clients"></a>配置用户 VPN 客户端

若要进行连接，需要下载 Azure VPN 客户端，并在要连接到 VNet 的每台计算机上导入在前面步骤中下载的 VPN 客户端配置文件。

> [!NOTE]
> Azure AD 身份验证仅支持用于 OpenVPN®协议连接。
>

#### <a name="to-download-the-azure-vpn-client"></a>下载 Azure VPN 客户端

使用[此链接](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab)下载 Azure VPN 客户端。

#### <a name="import"></a>导入客户端配置文件

1. 在页面上，选择“导入”。 

    ![进口](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. 浏览到 XML 配置文件并将其选中。 选择该文件后，选择“打开”。 

    ![进口](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. 指定配置文件的名称，并选择“保存”。 

    ![进口](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. 选择“连接”以连接到 VPN。 

    ![进口](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. 连接后，图标将变为绿色并指示“已连接”。 

    ![进口](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>删除客户端配置文件

1. 选择要删除的客户端配置文件旁边的省略号图标 (...)。 然后选择“删除”  。

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. 选择“删除”以删除配置文件。 

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>诊断连接问题

1. 若要诊断连接问题，可以使用“诊断”工具。  选择要诊断的 VPN 连接旁边的省略号图标 (...) 以显示菜单。 然后选择“诊断”。 

    ![诊断](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. 在“连接属性”页上，选择“运行诊断”。  

    ![诊断](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. 使用凭据登录。

    ![诊断](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. 查看诊断结果。

    ![诊断](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>查看虚拟 WAN

1. 导航到虚拟 WAN。
2. 在“概述”页上，地图中的每个点表示一个中心。 将鼠标悬停在任一点上可以查看中心运行状况的摘要。
3. 在“中心和连接”部分，可以查看中心状态、站点、区域、VPN 连接状态和传入与传出字节数。

## <a name="viewhealth"></a>查看资源运行状况

1. 导航到 WAN。
2. 在“WAN”页上的“支持 + 故障排除”部分，单击“运行状况”并查看资源。  


## <a name="cleanup"></a>清理资源

如果不再需要这些资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 删除资源组及其包含的所有资源。 将“myResourceGroup”替换为资源组的名称，并运行以下 PowerShell 命令：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
