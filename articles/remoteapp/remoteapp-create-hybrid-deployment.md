---
title: "如何创建 Azure RemoteApp 的混合集合 | Microsoft Docs"
description: "了解如何创建连接到您的内部网络的 RemoteApp 的部署。"
services: remoteapp
documentationcenter: 
author: lizap
manager: mbaldwin
editor: 
ms.assetid: 08ea0ce3-3a2c-4ddf-9394-6d75c8030cb1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 79434166999d4f847f28cf6a05a21f1cd002d75f


---
# <a name="how-to-create-a-hybrid-collection-for-azure-remoteapp"></a>如何创建 Azure RemoteApp 的混合集合
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

有两种类型的 Azure RemoteApp 集合：

* 云：完全驻留在 Azure 中。 你可以选择在云中保存所有数据（即仅限云集合），或者将你的集合连接到 VNET，并将数据保存在其中。   
* 混合：包括一个用于本地访问的虚拟网络，这需要使用 Azure AD 和本地 Active Directory 环境

你不知道你需要什么？ 查看 [Azure RemoteApp 需要哪种集合](remoteapp-collections.md)。

本教程将指导你完成创建混合收集的过程。 该过程有八个步骤︰

1. 决定要将哪个[映像](remoteapp-imageoptions.md)用于你的集合。 可以创建自定义映像，或者可以使用订阅附带的 Microsoft 映像之一。
2. 设置虚拟网络。 查看 [VNET 规划](remoteapp-planvnet.md)和[调整大小](remoteapp-vnetsizing.md)信息。
3. 创建集合。
4. 将集合加入本地域。
5. 将模板映像添加到你的集合。
6. 配置目录同步。 Azure RemoteApp 要求你通过以下两种方式之一与 Azure Active Directory 进行集成：1) 配置带有“密码同步”选项的“Azure Active Directory 同步”；2) 配置不带有“密码同步”选项的“Azure Active Directory 同步”，但使用的域是与 AD FS 联合的。 查看[带有 RemoteApp 的 Active Directory 配置信息](remoteapp-ad.md)。
7. 发布 RemoteApp 应用。
8. 配置用户访问权限。

**开始之前**

在创建集合之前，需要执行以下操作：

* [注册](https://azure.microsoft.com/services/remoteapp/) Azure RemoteApp。
* 在 Active Directory 中创建要用作 Azure RemoteApp 服务帐户的用户帐户。 限制此帐户的权限，以便它可以仅将计算机加入到域。
* 收集有关你的本地网络的信息︰IP 地址信息和 VPN 设备详细信息。
* 安装 [Azure PowerShell](../powershell-install-configure.md) 模块。
* 收集有关你想要授予访问权限的用户的信息。 你将需要每个用户的 Azure Active Directory 用户主体名称 (例如，name@contoso.com)。 请确保 UPN 在 Azure AD 和 Active Directory 之间匹配。
* 选择模板映像。 Azure RemoteApp 模板映像包含你希望为用户发布的应用和程序。 有关详细信息，请参阅 [Azure RemoteApp 映像选项](remoteapp-imageoptions.md)。
* 想要使用 Office 365 ProPlus 映像？ 请查看[此处](remoteapp-officesubscription.md)的信息。
* [为 RemoteApp 配置 Active Directory](remoteapp-ad.md)。

## <a name="step-1-set-up-your-virtual-network"></a>步骤 1：设置虚拟网络
你可以部署使用现有 Azure 虚拟网络的混合集合，也可以创建新的虚拟网络。 虚拟网络允许您的用户通过 RemoteApp 远程资源访问本地网络上的数据。 通过使用 Azure 虚拟网络，你的集合可以对部署到该虚拟网络的其他 Azure 服务和虚拟机进行直接的网络访问。

请确保在创建 VNET 之前，先查看 [VNET 规划](remoteapp-planvnet.md)和 [VNET 大小](remoteapp-vnetsizing.md)信息。

### <a name="create-an-azure-vnet-and-join-it-to-your-active-directory-deployment"></a>创建一个 Azure VNET，并将它加入 Active Directory 部署
首先创建一个[虚拟网络](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。 这是在 Azure 门户中的“**网络**”选项卡上完成的。 你需要将你的虚拟网络连接到与 Azure Active Directory 租户同步的 Active Directory 部署。

有关详细信息，请参阅[使用 Azure 门户创建虚拟网络](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。

### <a name="make-sure-your-virtual-network-is-ready-for-azure-remoteapp"></a>确保你的虚拟网络已经为 Azure RemoteApp 准备就绪。
在你创建集合之前，让我们确保新的虚拟网络已准备就绪。 可以通过执行以下操作来验证是否已准备就绪︰

1. 在你刚刚为 RemoteApp 创建的虚拟网络的子网内，创建一台 Azure 虚拟机。
2. 使用远程桌面连接到虚拟机。 （单击“**连接**”。）
3. 将它加入你希望用于 RemoteApp 的同一 Active Directory 部署。

这样是否可以工作？ 你的虚拟网络和子网已经为 Azure RemoteApp 准备就绪！

你可以在[此处](https://msdn.microsoft.com/library/azure/jj156003.aspx)找到有关创建 Azure 虚拟机和使用远程桌面连接到它们的详细信息。

## <a name="step-2-create-an-azure-remoteapp-collection"></a>步骤 2：创建 Azure RemoteApp 集合
1. 在“[Azure 门户](http://manage.windowsazure.com)”中，请转到 Azure RemoteApp 页面。
2. 单击“**新建 > 使用 VNET 创建**”。
3. 输入集合的名称。
4. 选择你想要使用的计划 - 标准或基本。
5. 从下拉列表中选择你的 VNET，然后选择你的子网。
6. 选择以将其加入你的域。
7. 单击“**创建 RemoteApp 集合**”。

创建 Azure RemoteApp 集合后，双击该集合的名称。 此时将出现“**快速启动**”页面，在这里你可以完成集合的配置。

出现了错误？ 查看[混合集合故障排除信息](remoteapp-hybridtrouble.md)。

## <a name="step-3-link-your-collection-to-the-local-domain"></a>步骤 3：将集合链接到本地域
1. 在“**快速启动**”页上，单击“**加入本地域”**。
2. 将 Azure RemoteApp 服务帐户添加到本地 Active Directory 域。 您将需要域名、组织单位、服务帐户的用户名和密码。
   
    如果你遵循[为 Azure RemoteApp 配置 Active Directory](remoteapp-ad.md) 中的步骤操作，就会收集到此信息。

## <a name="step-4-link-to-an-azure-remoteapp-image"></a>步骤 4：链接到 Azure RemoteApp 映像
Azure RemoteApp 模板映像包含你想要与用户共享的程序。 你可以创建新的[模板映像](remoteapp-imageoptions.md)或链接到现有映像（已导入或上载到 Azure RemoteApp 的映像）。 你还可以链接到其中一个包含 Office 365 或 Office 2013（供试用）程序的 Azure RemoteApp [模板映像](remoteapp-images.md)。

如果要上载新映像，你需要输入名称，并选择映像的位置。 在向导的下一页上，你将看到一组 PowerShell cmdlet - 从提升的 Windows PowerShell 提示符复制并运行这些 cmdlet 以上载指定的映像。

如果要链接到现有的模板映像，则只需指定映像名称、位置和关联的 Azure 订阅。

## <a name="step-5-configure-active-directory-directory-synchronization"></a>步骤 5：配置 Active Directory 目录同步
Azure RemoteApp 要求你通过以下两种方式之一与 Azure Active Directory 进行集成：1) 配置带有“密码同步”选项的“Azure Active Directory 同步”；2) 配置不带有“密码同步”选项的“Azure Active Directory 同步”，但使用的域是与 AD FS 联合的。

查看 [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) -这篇文章可帮助你用 4 个步骤完成目录集成的设置。

有关规划信息和详细步骤，请参阅[目录同步路线图](http://msdn.microsoft.com//library/azure/hh967642.aspx)。

## <a name="step-6-publish-apps"></a>步骤 6：发布应用
Azure RemoteApp 应用是你为用户提供的应用或程序。 它位于你为该集合上载的模板映像中。 当用户访问某个应用时，该应用就像是在它们的本地环境中运行，但实际上它是在 Azure 中运行。

你需要先发布应用，然后你的用户才可以访问它们 – 这样，你的用户就可以通过远程桌面客户端来访问这些应用。

你可以将多个应用发布到你的集合中。 从发布页中，单击“**发布**”以添加应用。 你可以从模板映像的“**开始**”菜单进行发布，也可以通过在模板映像上为应用指定路径进行发布。 如果你选择从“**开始**”菜单添加，请选择要添加的程序。 如果你选择提供应用的路径，请提供应用的名称，以及应用在模板映像上的安装路径。

## <a name="step-7-configure-user-access"></a>步骤 7：配置用户访问权限
至此，你已创建了集合，接下来需要添加能够使用你的远程资源的用户。 你提供访问权限的用户需要在 Active Directory 租户中存在，此租户与你用来创建此 Azure RemoteApp 集合的订阅相关联。

1. 在“快速入门”页上，单击“**配置用户访问权限**”。
2. 输入工作帐户（来自 Active Directory）或想要授予访问权限的 Microsoft 帐户。
   
   **说明：**
   
   确保你使用的是 “user@domain.com” 格式。
   
   如果你在集合中使用 Office 365 ProPlus，则必须为你的用户使用 Active Directory 标识。 这有助于验证授权。
3. 对用户进行验证后，请单击“**保存**”。

## <a name="next-steps"></a>后续步骤
全部完成 - 你已成功创建并部署了 Azure RemoteApp 混合集合。 下一步是让您的用户下载并安装远程桌面客户端。 你可以在 Azure RemoteApp 的“快速入门”页上找到客户端的 URL。 然后，让用户登录到客户端并访问你发布的应用。

### <a name="help-us-help-you"></a>请帮助我们改进，以便为你提供更好的帮助和支持
是否知道除了给这篇文章评级和在下面发表评论，还可以对文章本身进行更改？ 缺了什么？ 出现了错误？ 是否编写了令人困惑的内容？ 向上滚动并单击“在 GitHub 上编辑”  进行更改 - 更改的内容会发送给我们进行审阅，一旦签核，便可以在此处看到这些更改和改进。




<!--HONumber=Nov16_HO3-->


