---
title: "排查创建 RemoteApp 混合集合时遇到的问题 | Microsoft Docs"
description: "了解如何排查 RemoteApp 混合集合创建失败问题"
services: remoteapp
documentationcenter: 
author: vkbucha
manager: mbaldwin
ms.assetid: b32033ee-8d52-4e74-bb78-86ca873c34e2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a486dcb3f994cd78311ee86521a6792a4d57438e
ms.lasthandoff: 03/31/2017


---
# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>排查创建 Azure RemoteApp 混合集合时遇到的问题
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

混合集合托管于 Azure 云中，它不仅在 Azure 云中存储数据，而且允许用户访问本地网络中存储的数据和资源。 用户可通过使用其与 Azure Active Directory 同步或联合的企业凭据登录来访问应用。 你可以部署使用现有 Azure 虚拟网络的混合集合，也可以创建新的虚拟网络。 我们建议你创建或使用 CIDR 范围足够大的虚拟网络子网，来获得 Azure RemoteApp 的预期未来增长。

尚未创建集合？ 请参阅[创建混合集合](remoteapp-create-hybrid-deployment.md)了解相关步骤。

如果你无法创建集合，或如果集合未按你所认为的应有方式工作，请查看以下信息。

## <a name="your-image-is-invalid"></a>你的映像无效
如果在等待 Azure 设置你的集合时，你看到类似“GoldImageInvalid”的消息，这意味着你的模板映像不符合[定义的映像要求](remoteapp-imagereqs.md)。 因此，请继续阅读这些[要求](remoteapp-imagereqs.md)、修复你的映像，并尝试再次创建你的集合。

## <a name="does-your-vnet-have-network-security-groups-defined"></a>你的 VNET 是否定义了网络安全组？
如果在用于集合的子网上定义了网络安全组，请确保可从你的子网内部访问这些 [URL 和端口](remoteapp-ports.md)。

你可以将其他网络安全组添加到由你在子网中部署的 VM 以加强控制。

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>正在使用你自己的 DNS 服务器？ 是否可从你的 VNET 子网中访问它们？
> [!NOTE]
> 你必须确保你的 VNET 中的 DNS 服务器始终启动并始终能够解析托管在 VNET 中的虚拟机。 不要为此使用 Google DNS。
> 
> 

对于混合集合，你将使用你自己的 DNS 服务器。 创建虚拟网络时，你在网络配置架构中或通过管理门户指定这些服务器。 以故障转移方式（而不是轮循机制）按照指定的顺序使用 DNS 服务器。  
请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)，确保正确配置了 DNS 服务器。

请确保你的集合的 DNS 服务器都可以访问，并且可从为此集合指定的 VNET 子网中获取。

例如：

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![定义 DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>你是否正在使用你的集合中的 Active Directory 域控制器？
当前仅有一个 Active Directory 域可以与 Azure RemoteApp 相关联。 混合集合仅支持已使用 Windows Server Active Directory 部署中的 DirSync 工具同步的 Azure Active Directory 帐户；具体而言，已与密码同步选项同步或已与配置的 Active Directory 联合身份验证服务 (AD FS) 联盟同步。 你需要创建与你的本地域的 UPN 域后缀匹配的自定义域并设置目录集成。

有关详细信息，请参阅[为 Azure RemoteApp 配置 Active Directory](remoteapp-ad.md)。

请确保提供的域详细信息有效，并且域控制器可从在用于 Azure RemoteApp 的子网中创建的 VM 中获取。 此外，还请确保提供的服务帐户凭据有权将计算机添加到提供的域，并确保提供的 AD 名称可以从 VNET 中提供的 DNS 解析。

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>创建你的集合时，指定了哪些域名？
你创建或添加的域名必须是内部域名（不是 Azure AD 域名），并且必须是可解析的 DNS 格式 (contoso.local)。 例如，你有一个 Active Directory 内部名称 (contoso.local) 和一个 Active Directory UPN (contoso.com) - 创建你的集合时必须使用内部名称。


