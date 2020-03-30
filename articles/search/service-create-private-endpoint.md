---
title: 为安全连接创建专用终结点
titleSuffix: Azure Cognitive Search
description: 在虚拟网络中设置专用终结点，以便安全连接到 Azure 认知搜索服务
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945825"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>为与 Azure 认知搜索的安全连接创建专用终结点（预览）

在本文中，使用门户创建新的 Azure 认知搜索服务实例，该实例无法通过公共 IP 地址访问。 接下来，在同一虚拟网络中配置 Azure 虚拟机，并使用它通过专用终结点访问搜索服务。

> [!Important]
> Azure 认知搜索的专用终结点支持可[应请求](https://aka.ms/SearchPrivateLinkRequestAccess)作为受限访问预览提供。 预览功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 
>
> 获得预览访问权限后，可以使用 Azure 门户或[管理 REST API 版本 2019-10-06-预览](https://docs.microsoft.com/rest/api/searchmanagement/)为服务配置专用终结点。
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>为什么要使用专用终结点进行安全访问？

Azure 认知搜索[的专用终结点](../private-link/private-endpoint-overview.md)允许虚拟网络上的客户端通过[专用链接](../private-link/private-link-overview.md)安全地访问搜索索引中的数据。 专用终结点使用[虚拟网络地址空间](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)中的 IP 地址进行搜索服务。 客户端和搜索服务之间的网络流量通过虚拟网络和 Microsoft 骨干网络上的专用链路遍历，消除了公共 Internet 的暴露。 有关支持专用链接的其他 PaaS 服务的列表，请查看产品文档中[的可用性部分](../private-link/private-link-overview.md#availability)。

搜索服务的专用终结点使您能够：

- 阻止搜索服务的公共终结点上的所有连接。
- 通过阻止数据从虚拟网络进行外泄，提高虚拟网络的安全性。
- 使用[VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[快速路由](../expressroute/expressroute-locations.md)与专用对等互连连接到虚拟网络的本地网络安全地连接到搜索服务。

> [!NOTE]
> 预览版当前存在一些限制，您应该注意：
> * 仅适用于**基本**层上的搜索服务。 
> * 在美国西部 2、美国中西部、美国东部、美国中南部、澳大利亚东部和澳大利亚东南部地区提供。
> * 当服务终结点是私有的时，某些门户功能将禁用。 您可以查看和管理服务级别信息，但出于安全原因，对索引数据和服务中各种组件（如索引、索引器和技能集定义）的门户访问受到限制。
> * 当服务终结点是私有的时，必须使用[搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)将文档上载到索引。
> * 您必须使用以下链接才能在 Azure 门户中查看专用终结点支持选项：https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>请求访问权限 

单击[请求访问权限](https://aka.ms/SearchPrivateLinkRequestAccess)以注册此预览功能。 该窗体请求有关您、您的公司和一般网络拓扑的信息。 一旦我们查看您的请求，您将收到一封包含其他说明的确认电子邮件。

## <a name="create-the-virtual-network"></a>创建虚拟网络

在本节中，您将创建一个虚拟网络和子网来承载将用于访问搜索服务的专用终结点的 VM。

1. 在 Azure 门户主页选项卡中，选择 **"创建资源** > **网络** > **虚拟网络**"。

1. 在“创建虚拟网络”**** 中，输入或选择以下信息：

    | 设置 | “值” |
    | ------- | ----- |
    | “属性” | 输入*我的虚拟网络* |
    | 地址空间 | 输入*10.1.0.0/16* |
    | 订阅 | 选择订阅|
    | 资源组 | 选择 **"创建新**"，输入*我的资源组*，然后选择 **"确定"** |
    | 位置 | 选择**美国西部**或您正在使用的任何区域|
    | 子网 - 名称 | 输入*我的子网* |
    | 子网 - 地址范围 | 输入*10.1.0.0/24* |
    |||

1. 将其余的设置保留默认值，然后选择“创建”****。


## <a name="create-a-search-service-with-a-private-endpoint"></a>使用专用终结点创建搜索服务

在本节中，您将使用专用终结点创建新的 Azure 认知搜索服务。 

1. 在 Azure 门户中屏幕的左上角，选择 **"创建资源** > **Web** > **Azure 认知搜索**"。

1. 在新**搜索服务 - 基础知识中**，输入或选择此信息：

    | 设置 | “值” |
    | ------- | ----- |
    | **项目详情** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择**我的资源组**。 已在上一部分创建此内容。|
    | **实例详细信息** |  |
    | 代码 | 输入唯一名称。 |
    | 位置 | 选择请求访问此预览功能时指定的区域。 |
    | 定价层 | 选择 **"更改定价层**"并选择 **"基本**"。 预览需要此层。 |
    |||
  
1. 选择**下一个：缩放**。

1. 将值保留为默认值，然后选择 **"下一步：网络**"。

1. 在 **"新搜索服务 - 网络"中**，选择 **"专用**"以进行**端点连接（数据）。**

1. 在 **"新搜索服务 - 网络**"中，选择 "**专用终结点**"下**添加**。 

1. 在“创建专用终结点”**** 中，输入或选择以下信息：

    | 设置 | “值” |
    | ------- | ----- |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择**我的资源组**。 已在上一部分创建此内容。|
    | 位置 | 选择“美国西部”****。|
    | “属性” | 输入  *myPrivateEndpoint*。  |
    | 目标子资源 | 离开默认**搜索服务**。 |
    | **网络** |  |
    | 虚拟网络  | 从资源组“myResourceGroup”** 中选择“MyVirtualNetwork” **。 |
    | 子网 | 选择“mySubnet”。 ** |
    | **专用 DNS 集成** |  |
    | 与专用 DNS 区域集成  | 保留默认值“是”****。 |
    | 专用 DNS 区域  | 保留默认值 [ （新） privatelink.search.windows.net]。 |
    |||

1. 选择“确定”。 

1. 选择“查看 + 创建”****。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置****。 

1. 当您看到**验证传递**的消息时，选择 **"创建**"。 

1. 完成新服务的预配后，浏览到您刚刚创建的资源。

1. 从左侧内容菜单中选择 **"键**"。

1. 在连接到服务时，请复制**主管理密钥**，以便以后使用。

## <a name="create-a-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户中屏幕的左上角，选择 **"创建资源** > **计算** > **虚拟机**"。

1. 在“创建虚拟机 - 基本信息”**** 中，输入或选择以下信息：

    | 设置 | “值” |
    | ------- | ----- |
    | **项目详情** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择**我的资源组**。 已在上一部分创建此内容。  |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 *myVm*。 |
    | 区域 | 选择**美国西部**或您正在使用的任何区域。 |
    | 可用性选项 | 保留默认值“不需要基础结构冗余”****。 |
    | 图像 | 选择“Windows Server 2019 Datacenter”。**** |
    | 大小 | 保留默认值“标准 DS1 v2”****。 |
    | **管理员帐户** |  |
    | 用户名 | 输入所选用户名。 |
    | 密码 | 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 确认密码 | 重新输入密码。 |
    | **入站端口规则** |  |
    | 公共入站端口 | 保留默认 **"允许所选端口**"。 |
    | 选择入站端口 | 保留默认**RDP （3389）。** |
    | **节省资金** |  |
    | 已有 Windows 许可证？ | 保留默认值“否”****。 |
    |||

1. 选择 **"下一步"：磁盘**。

1. 在 **"创建虚拟机磁盘"中**，保留默认值并选择 **"下一步：网络**"。

1. 在“创建虚拟机 - 基本信息”**** 中，选择以下信息：

    | 设置 | “值” |
    | ------- | ----- |
    | 虚拟网络 | 保留默认值“MyVirtualNetwork”****。  |
    | 地址空间 | 保留默认值“10.1.0.0/24”。****|
    | 子网 | 保留默认值“mySubnet (10.1.0.0/24)”。****|
    | 公共 IP | 保留默认值“(new) myVm-ip”****。 |
    | 公共入站端口 | 选择“允许所选端口”****。 |
    | 选择入站端口 | 选择“HTTP”和“RDP”。********|
    ||

1. 选择“查看 + 创建”****。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置****。

1. 当您看到**验证传递**的消息时，选择 **"创建**"。 


## <a name="connect-to-the-vm"></a>连接到 VM

下载，然后连接到 VM *myVm，* 如下所示：

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。**** 选择“连接”按钮后，“连接到虚拟机”随即打开********。

1. 选择**下载 RDP 文件**。 Azure 会创建远程桌面协议 (*.rdp*) 文件，并将其下载到计算机。

1. 打开下载的 .rdp* 文件。

    1. 出现提示时，选择“连接”****。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 您可能需要选择**更多选项** > **"使用其他帐户**"来指定创建 VM 时输入的凭据。

1. 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”********。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  


## <a name="test-connections"></a>测试连接

在本节中，您将验证对搜索服务的专用网络访问，并私下连接到专用终结点。

回想一下，与搜索服务的所有交互都需要搜索 REST [API](https://docs.microsoft.com/rest/api/searchservice/)。 此预览不支持门户和 .NET SDK。

1. 在  *myVM* 的远程桌面中打开 PowerShell。

1. 输入"nsup_搜索服务名称"*search.windows.net'

    将收到类似于下面的消息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. 从 VM 连接到搜索服务并创建索引。 您可以按照此[快速入门](search-get-started-postman.md)在 Postman 中使用 REST API 在服务中创建新的搜索索引。 设置来自 Postman 的请求需要搜索服务终结点（https：//[搜索服务名称]search.windows.net）和您在上一步中复制的管理员 api 键。

1. 从 VM 完成快速入门是您确认服务已完全正常运行。

1. 关闭远程桌面连接到 *myVM。* 

1. 要验证服务在公共终结点上不可访问，请打开本地工作站上的 Postman，并尝试快速入门中的头几个任务。 如果收到远程服务器不存在的错误，则已成功为搜索服务配置专用终结点。

## <a name="clean-up-resources"></a>清理资源 
使用专用终结点、搜索服务和 VM 完成后，请删除资源组及其包含的所有资源：
1. 在门户顶部的 **"搜索"** 框中输入 *"我的资源组* "，然后从搜索结果中选择 *"我的资源组* "。 
1. 选择“删除资源组”****。 
1. 输入 *"资源组*  **"以键入资源组名称**，然后选择 **"删除**"。

## <a name="next-steps"></a>后续步骤
在本文中，您在虚拟网络和具有专用终结点的搜索服务上创建了 VM。 您从 Internet 连接到 VM，并使用专用链接安全地连接到搜索服务。 若要详细了解专用终结点，请参阅 [什么是 Azure 专用终结点？](../private-link/private-endpoint-overview.md)。
