---
title: 创建安全连接的专用终结点
titleSuffix: Azure Cognitive Search
description: 目前在预览版中，你可以使用专用终结点和安全 VNet 连接限制对搜索服务终结点的访问。
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 0ed6319a33fc3db0b3144d91f4be10b7ab6121d7
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75865007"
---
# <a name="restrict-access-to-azure-cognitive-search-using-private-endpoint-and-a-virtual-network-connection"></a>使用专用终结点和虚拟网络连接限制对 Azure 认知搜索的访问。

> [!IMPORTANT] 
> 对专用终结点的支持目前以受限访问预览版的形式提供。 此预览版仅适用于 "**基本" 层**上的搜索服务。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-10-01-Preview](search-api-preview.md)提供此功能。 目前没有门户或 .NET SDK 支持。

本文介绍如何创建可通过安全连接访问的新搜索服务，无从公共 IP 地址进行访问。 允许从部署在与服务相同的虚拟网络中的 Azure 虚拟机进行客户端连接。

## <a name="about-private-endpoint-support"></a>关于专用终结点支持

Azure 认知搜索的[专用终结点](../private-link/private-endpoint-overview.md)允许虚拟网络上的客户端通过[专用链接](../private-link/private-link-overview.md)安全地访问数据。 专用终结点使用来自[虚拟网络地址空间](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)的 IP 地址来搜索服务。 客户端与搜索服务之间的网络流量将在 Microsoft 主干网络上遍历虚拟网络和专用链接，从而消除公开 internet 的风险。 有关支持专用链接的 PaaS 服务的列表，请查看产品文档中的 "[可用性" 部分](../private-link/private-link-overview.md#availability)。

搜索服务的专用终结点使你能够：

+ 阻止在公共终结点上搜索服务的所有连接。
+ 通过使你能够阻止渗透虚拟网络中的数据，提高虚拟网络的安全性。
+ 使用[VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[ExpressRoutes](../expressroute/expressroute-locations.md)与专用对等互连，从连接到虚拟网络的本地网络安全连接到搜索服务。

> [!NOTE]
> 当服务终结点为私有时，某些门户功能处于禁用状态。 你将能够查看和管理服务级别信息，但出于安全考虑，出于安全原因，对索引数据和服务中的各种组件（如索引、索引器和技能组合定义）的门户访问是受限的。

## <a name="request-access"></a>请求访问权限 

单击 "[请求访问权限](https://aka.ms/SearchPrivateLinkRequestAccess)" 注册此预览功能。 此窗体请求有关您、您的公司和常规网络拓扑的信息。 查看请求后，将收到一封包含其他说明的确认电子邮件。

## <a name="create-a-vm"></a>创建 VM
在本部分中，你将创建一个虚拟网络和子网来托管将用于访问搜索服务的专用终结点的 VM。

### <a name="set-up-the-virtual-network"></a>设置虚拟网络
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左上方，选择 "**创建资源**" > **网络** > "**虚拟网络**"。
1. 在“创建虚拟网络”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入*MyVirtualNetwork* |
    | 地址空间 | 输入*10.1.0.0/16* |
    | 订阅 | 选择订阅|
    | 资源组 | 选择 "**新建**"，输入*myResourceGroup*，然后选择 **"确定"** |
    | 位置 | 选择 "**美国西部**" 或任何所使用的区域|
    | 子网 - 名称 | 输入*mySubnet* |
    | 子网 - 地址范围 | 输入*10.1.0.0/24* |
    |||

1. 将其余的设置保留默认值，然后选择“创建”。


### <a name="create-a-virtual-machine"></a>创建虚拟机

1. 在主门户页面的左上方，选择 "**创建资源**" > **计算**" > "**虚拟机**"。

1. 在“创建虚拟机 - 基本信息”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。  |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 *myVm*。 |
    | 地区 | 选择 "**美国西部**" 或任何所使用的区域。 |
    | 可用性选项 | 保留默认值“不需要基础结构冗余”。 |
    | 图像 | 选择“Windows Server 2019 Datacenter”。 |
    | 大小 | 保留默认值“标准 DS1 v2”。 |
    | **管理员帐户** |  |
    | 用户名 | 输入所选用户名。 |
    | 密码 | 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 确认密码 | 重新输入密码。 |
    | **入站端口规则** |  |
    | 公共入站端口 | 保留默认值“无”。 |
    | **节省资金** |  |
    | 已有 Windows 许可证？ | 保留默认值“否”。 |
    |||

1. 选择 "**下一步：磁盘**"。

1. 在 "**创建虚拟机-磁盘**" 中，保留默认值并选择 "**下一步：网络**"。

1. 在“创建虚拟机 - 基本信息”中，选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 虚拟网络 | 保留默认值“MyVirtualNetwork”。  |
    | 地址空间 | 保留默认值“10.1.0.0/24”。|
    | 子网 | 保留默认值“mySubnet (10.1.0.0/24)”。|
    | 公共 IP | 保留默认值“(new) myVm-ip”。 |
    | 公共入站端口 | 选择“允许所选端口”。 |
    | 选择入站端口 | 选择“HTTP”和“RDP”。|
    ||

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

1. 看到“验证通过”消息时，选择“创建”。


## <a name="create-your-search-service-with-a-private-endpoint"></a>使用私有终结点创建搜索服务

在本部分中，将创建一个具有专用终结点的新 Azure 认知搜索服务。 

1. 在主门户页面的左上方，选择 "**创建资源** > **Web** > **Azure 认知搜索**"。

1. 在**新的搜索服务-基础知识**中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。|
    | **实例详细信息** |  |
    | URL | 输入唯一名称。 |
    | 位置 | 选择在请求访问此预览功能时指定的区域。 |
    | 定价层 | 选择 "**更改定价层**" 并选择 "**基本**"。 预览需要此层。 |
    |||
  
1. 选择**下一步：缩放**。

1. 将值保留为默认值，然后选择 "**下一步：网络**"。

1. 在 "**新建搜索服务-网络**" 中，选择 "**专用**" 作为**终结点连接（数据）** 。

1. 在 "**新建搜索服务**" 中，选择 "在**私有终结点**下**添加 +** "。 

1. 在 "**创建专用终结点**" 中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。|
    | 位置 | 选择 "**美国西部**"。|
    | 名称 | 输入 *myPrivateEndpoint*。  |
    | 目标子资源 | 保留默认**searchService**。 |
    | **网络** |  |
    | 虚拟网络  | 从资源组*myResourceGroup*中选择 " *MyVirtualNetwork* "。 |
    | 子网 | 选择“mySubnet”。 ** |
    | **专用 DNS 集成** |  |
    | 与专用 DNS 区域集成  | 保留默认值 **"是"** 。 |
    | 专用 DNS 区域  | 保留默认值 * * （New） privatelink.search.windows.net * *。 |
    |||

1. 选择“确定”。 

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。 

1. 看到“验证通过”消息时，选择“创建”。 
1. 创建服务后，浏览到刚刚创建的资源。
1. 从左侧内容菜单中选择 "**密钥**"。
1. 复制**主管理密钥**，以便在下一步中使用。

 
## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

从 Internet 连接到 VM *myVm*，如下所示：

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。 选择“连接”按钮后，“连接到虚拟机”随即打开。

1. 选择“下载 RDP 文件”。 Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开下载的 .rdp* 文件。

    1. 出现提示时，选择“连接”。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据。

1. 选择“确定”。

1. 登录时，可能会收到证书警告。 如果收到证书警告，请选择“确定”或“继续”。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  


## <a name="access-the-search-service-privately-from-the-vm"></a>从 VM 私下访问搜索服务

在本部分中，你将验证对搜索服务的专用网络访问，并使用专用终结点将专用连接到存储帐户。

1. 在  *myVM* 的远程桌面中打开 PowerShell。

1. 输入 "nslookup [search service name]. search. net"

    将收到类似于下面的消息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```
1. 按照此 VM 的以下[快速入门](search-get-started-postman.md)，使用 REST API 在 Postman 中的服务中创建新的搜索索引。

1. 在本地工作站上的 Postman 中尝试多个相同的请求。

1. 如果能够从 VM 完成快速入门，但收到错误消息，指出远程服务器不存在于本地工作站上，则表明已成功配置搜索服务的专用终结点。

1. 关闭与 *myVM*的远程桌面连接。 

## <a name="clean-up-resources"></a>清理资源 

使用完专用终结点、搜索服务帐户和 VM 后，请删除资源组及其包含的所有资源： 

1. 在门户顶部的“搜索”框中输入“myResourceGroup” **  ，然后从搜索结果中选择“myResourceGroup”。 **   
1. 选择“删除资源组”。 
1. 输入 " *myResourceGroup*  **" 以键入资源组名称**，然后选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在虚拟网络上创建虚拟机，以及如何使用专用终结点创建搜索服务。 你从 internet 连接到 VM，并使用专用链接安全地传达给搜索服务。 

> [!div class="nextstepaction"]
> [什么是 Azure 专用终结点？](../private-link/private-endpoint-overview.md)。
