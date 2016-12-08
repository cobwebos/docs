---
title: "使用 Azure 流量管理器在 Azure 中部署高可用性跨地理区域 AD FS | Microsoft Docs"
description: "在本文档中，你将学习如何在 Azure 中部署 AD FS 以实现高可用性。"
keywords: "使用 Azure 流量管理器部署 Ad fs, 使用 Azure 流量管理器部署 adfs, 多个数据中心, 地理区域数据中心, 多个地理区域数据中心, 在 Azure 中部署 AD FS, 部署 Azure ADFS, Azure ADFS, Azure AD FS, 部署 ADFS, 部署 AD FS, Azure 中的 ADFS, 在 Azure 中部署 ADFS, 在 Azure 中部署 AD FS, ADFS Azure, AD FS 简介, Azure, Azure 中的 AD FS, IaaS, ADFS, 将 ADFS 移到 Azure"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: a14bc870-9fad-45ed-acd5-a90ccd432e54
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: anandy;billmath
translationtype: Human Translation
ms.sourcegitcommit: 3170abb4f9bd7f7996b1c0dd2e20f648ea1b9fe5
ms.openlocfilehash: e2125c56a958e8ed6b02ec7e92dd7cf4dcf326f3


---
# <a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>使用 Azure 流量管理器在 Azure 中部署高可用性跨地理区域 AD FS
[AD FS deployment in Azure](active-directory-aadconnect-azure-adfs.md) （Azure 中的 AD FS 部署）提供了有关如何在 Azure 中为组织部署简单 AD FS 基础结构的分步指导。 本文提供后续步骤，使用 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)在 Azure 中创建跨地理区域的 AD FS 部署。 Azure 流量管理器使用各种可用的路由方法来应对基础结构的不同需求，有助于为组织创建分布各地的高可用性和高性能 AD FS 基础结构。

高可用性的跨地理区域 AD FS 基础结构能够：

* **消除单点故障** ：使用 Azure 流量管理器的故障转移功能，即使在全球的一个数据中心服务中断，也仍可以实现高可用性的 AD FS 基础结构
* **提高性能** ：可以使用本文中建议的部署，提供高性能 AD FS 基础结构来帮助用户加速身份验证过程。 

## <a name="design-principles"></a>设计原理
![整体设计](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

基本设计原理与“AD FS deployment in Azure”（Azure 中的 AD FS 部署）一文中所述的设计原理相同。 上图显示了将基本部署扩展到另一个地理区域的简单扩展。 下面是将部署扩展到新地理区域时所要考虑的一些要点

* **虚拟网络：** 应在要部署其他 AD FS 基础结构的地理区域中创建新的虚拟网络。 在上图中，Geo1 VNET 和 Geo2 VNET 是每个地理区域中的两个虚拟网络。
* **新地理区域 VNET 中的域控制器和 AD FS 服务器：** 建议在新地理区域中部署域控制器，这样，新区域中的 AD FS 服务器就不需要联系另一个远端网络中的域控制器来完成身份验证，从而可以提高性能。
* **存储帐户：** 存储帐户与某个区域关联。 由于要在新地理区域中部署计算机，因此必须创建要在该区域中使用的新存储帐户。  
* **网络安全组：** 与存储帐户一样，在一个区域中创建的网络安全组不能在另一个地理区域中使用。 因此，需要为新地理区域中的 INT 和外围网络子网创建类似于第一个地理区域中的新网络安全组。
* **公共 IP 地址的 DNS 标签：** Azure 流量管理器只能通过 DNS 标签引用终结点。 因此，必须为外部负载平衡器的公共 IP 地址创建 DNS 标签。
* **Azure 流量管理器：** 使用 Microsoft Azure 流量管理器可以控制用户流量的分布，根据需要将用户流量分布到在全球不同数据中心运行的服务终结点。 Azure 流量管理器在 DNS 级别工作。 它使用 DNS 响应将最终用户流量定向到全球分布的终结点。 然后，客户端直接连接到这些终结点。 性能、加权和优先级均有不同的路由选项，可以轻松选择最符合组织需求的路由选项。 
* **两个区域之间的 VNet 到 VNet 连接：** 不需要在虚拟网络本身之间建立连接。 由于每个虚拟网络均可访问域控制器，并且本身都有 AD FS 和 WAP 服务器，因此不同区域中的虚拟网络之间不需要建立任何连接即可正常工作。 

## <a name="steps-to-integrate-azure-traffic-manager"></a>集成 Azure 流量管理器的步骤
### <a name="deploy-ad-fs-in-the-new-geographical-region"></a>在新地理区域中部署 AD FS
遵循 [AD FS deployment in Azure](active-directory-aadconnect-azure-adfs.md) （Azure 中的 AD FS 部署）中的步骤和指导，在新地理区域中部署相同的拓朴。

### <a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>面向 Internet 的（公共）负载平衡器中公共 IP 地址的 DNS 标签
如前所述，Azure 流量管理器只能通过 DNS 标签引用终结点，因此，请务必为外部负载平衡器的公共 IP 地址创建 DNS 标签。 以下屏幕截图显示如何配置公共 IP 地址的 DNS 标签。 

![DNS 标签](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

### <a name="deploying-azure-traffic-manager"></a>部署 Azure 流量管理器
遵循以下步骤创建流量管理器配置文件。 有关详细信息，也可以参阅 [Manage an Azure Traffic Manager profile](../traffic-manager/traffic-manager-manage-profiles.md)（管理 Azure 流量管理器配置文件）。

1. **创建流量管理器配置文件：** 为流量管理器配置文件指定唯一的名称。 配置文件的此名称是 DNS 名称的一部分，可用作流量管理器域名标签的前缀。 系统将此名称/前缀添加到 .trafficmanager.net，创建流量管理器的 DNS 标签。 以下屏幕截图显示设置为 mysts 的流量管理器 DNS 前缀，生成的 DNS 标签是 mysts.trafficmanager.net。 
   
    ![创建流量管理器配置文件](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
2. **流量路由方法：** 流量管理器中有三个可用的路由选项：
   
   * Priority 
   * 性能
   * 加权
     
     **性能** 是实现高响应度 AD FS 基础结构的建议选项。 但是，可以根据部署需求选择最合适的路由方法。 AD FS 功能不受所选路由选项的影响。 有关详细信息，请参阅 [Traffic Manager traffic routing methods](../traffic-manager/traffic-manager-routing-methods.md) （流量管理器流量路由方法）。 在上面的示例屏幕截图中，可以看到选择了 **性能** 方法。
3. **配置终结点：** 在流量管理器页中，单击终结点并选择“添加”。 此时将打开类似于以下屏幕截图的“添加终结点”页
   
   ![配置终结点](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
   
   对于不同的输入，请遵循以下指导：
   
   **类型：** 选择 Azure 终结点，因为指向的是 Azure 公共 IP 地址。
   
   **名称：** 创建要与终结点关联的名称。 这不是 DNS 名称，因此与 DNS 记录没有关系。
   
   **目标资源类型：** 选择公共 IP 地址作为此属性的值。 
   
   **目标资源：** 此选项用于选择订阅下面可用的不同 DNS 标签。 选择与你在配置的终结点对应的 DNS 标签。
   
   针对要让 Azure 流量管理器将流量路由到的每个地理区域添加终结点。
   有关如何在流量管理器中添加/配置终结点的详细信息和详细步骤，请参阅 [Add, disable, enable or delete endpoints](../traffic-manager/traffic-manager-endpoints.md)
4. **配置探测：** 在流量管理器页中，单击“配置”。 在配置页中，需要更改监视设置，以便在 HTTP 端口 80 和相对路径 /adfs/probe 上探测
   
    ![配置探测](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 
   
   > [!NOTE]
   > **确保完成配置时，终结点的状态为“联机”**。 如果所有终结点都处于“降级”状态，Azure 流量管理器会尽力尝试路由流量，但前提是诊断信息不正确，并且所有终结点均可访问。
   > 
   > 
5. **修改 Azure 流量管理器的 DNS 记录：** 联合身份验证服务应该是 Azure 流量管理器 DNS 名称的 CNAME。 在公共 DNS 记录中创建 CNAME，使尝试访问联合身份验证服务的用户确实可以访问 Azure 流量管理器。
   
    例如，若要将联合身份验证服务 fs.fabidentity.com 指向流量管理器，需要对 DNS 资源记录进行以下更新：
   
    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

## <a name="test-the-routing-and-ad-fs-sign-in"></a>测试路由和 AD FS 登录
### <a name="routing-test"></a>路由测试
一个很简单的路由测试方法就是尝试从每个地理区域中的计算机 ping 联合身份验证服务的 DNS 名称。 根据选择的路由方法，实际 ping 到的终结点将反映在 ping 显示结果中。 例如，如果选择了性能路由，则会访问最靠近客户端区域的终结点。 下面是两个来自两个不同区域客户端计算机的 ping 快照，一个位于东亚区域，一个位于美国西部。 

![路由测试](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

### <a name="ad-fs-sign-in-test"></a>AD FS 登录测试
测试 AD FS 的最简单方法是使用 IdpInitiatedSignon.aspx 页。 若要执行此操作，必须在 AD FS 属性中启用 IdpInitiatedSignOn。 请遵循以下步骤来验证你的 AD FS 设置

1. 使用 PowerShell 在 AD FS 服务器上运行以下 cmdlet，以将它设置为启用。 
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true
2. 从任何外部计算机访问 https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. 你应会看到如下所示的 AD FS 页：
   
    ![ADFS 测试 - 身份验证质询](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)
   
    成功登录后，该页将提供如下所示的成功消息：
   
    ![ADFS 测试 - 身份验证成功](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)

## <a name="related-links"></a>相关链接
* [Basic AD FS deployment in Azure](active-directory-aadconnect-azure-adfs.md)
* [Microsoft Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
* [流量管理器流量路由方法](../traffic-manager/traffic-manager-routing-methods.md)

## <a name="next-steps"></a>后续步骤
* [管理 Azure 流量管理器配置文件](../traffic-manager/traffic-manager-manage-profiles.md)
* [添加、禁用、启用或删除终结点](../traffic-manager/traffic-manager-endpoints.md) 




<!--HONumber=Nov16_HO4-->


