---
title: "如何创建 Azure RemoteApp 的云集合 | Microsoft Docs"
description: "了解如何创建将数据保存在 Azure 云中的 Azure RemoteApp 的部署。"
services: remoteapp
documentationcenter: 
author: lizap
manager: mbaldwin
editor: 
ms.assetid: 4d7c6956-7e4a-4a41-b7f2-7e5832bf01e3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 21d6cabf44d2a2b63b158f1ebd567cc8945e0333


---
# <a name="how-to-create-a-cloud-collection-of-azure-remoteapp"></a>如何创建 Azure RemoteApp 的云集合
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

有两种类型的 [Azure RemoteApp 集合](remoteapp-collections.md)： 

* 云：完全驻留在 Azure 中。 你可以选择在云中保存所有数据（即仅限云集合），或者将你的集合连接到 VNET，并将数据保存在其中。   
* 混合：包括一个用于本地访问的虚拟网络，这需要使用 Azure AD 和本地 Active Directory 环境。

本教程将逐步指导你完成创建云集合的过程。 有四个步骤： 

1. 创建 Azure RemoteApp 集合。
2. （可选）配置目录同步。 如果你使用的是 Azure AD + Active Directory，则必须将用户、联系人和密码从本地 Active Directory 同步到 Azure AD 租户。
3. 发布应用。
4. 配置用户访问权限。

**开始之前**

在创建集合之前，需要执行以下操作：

* [注册](https://azure.microsoft.com/services/remoteapp/) Azure RemoteApp。 
* 收集有关你想要授予访问权限的用户的信息。 这可以是用户的 Microsoft 帐户信息，或 Active Directory 工作帐户信息。
* 此过程假定你将使用作为你的订阅的一部分提供的模板映像之一，或者你已上载想要使用的模板映像。 如果需要上载不同的模板映像，可以从“模板映像”页面执行此操作。 只需单击“**上载模板映像**”，然后按照向导中的步骤进行操作。 
* 想要使用 Office 365 ProPlus 映像？ 请查看[此处](remoteapp-officesubscription.md)的信息。
* 想要提供自定义应用或 LOB 程序？ 请创建一个新的[映像](remoteapp-imageoptions.md)，并在云集合中加以使用。
* 确定是否需要连接到 VNET。 如果你选择连接到 VNET，请确保它符合[大小调整指南](remoteapp-vnetsizing.md)，以及其是否[可以连接到 RemoteApp](remoteapp-vnet.md)。 请查看 [VNET 规划文章](remoteapp-planvnet.md)了解更多信息。
* 如果你使用的是 VNET，请决定是否要将其加入到你的本地 Active Directory 域。

## <a name="step-1-create-a-cloud-collection---with-or-without-a-vnet"></a>步骤 1：创建云集合 - 不论是否有 VNET
请使用以下步骤**创建一个仅云集合**：

1. 在管理门户中，转到 RemoteApp 页面。
2. 单击“**新建 > 快速创建**”。
3. 输入集合的名称，然后选择你的区域。
4. 选择你想要使用的计划 - 标准或基本。
5. 选择要用于此集合的模板。 
   
    **提示：**RemoteApp 的订阅附带[模板映像](remoteapp-images.md)，其中包含 Office 365 或 Office 2013（供试用）程序，有些已发布（如 Word），有些即将发布。 你也可以创建一个新[映像](remoteapp-imageoptions.md)，并在云集合中使用它。
6. 单击“**创建 RemoteApp 集合**”。
   
    **重要：**配置集合可能最多需要 30 分钟。

创建 Azure RemoteApp 集合后，双击该集合的名称。 此时将出现“**快速入门**”页面，在这里你可以完成集合的配置。

请使用以下步骤创建**云 + VNET 集合**：

1. 在管理门户中，请转到 Azure RemoteApp 页面。
2. 单击“**新建**” > “**使用 VNET 创建**”。
3. 输入集合的名称。
4. 选择你想要使用的计划 - 标准或基本。
5. 选择已创建的 VNET。 不知道该怎么做？ 这些步骤目前都在[混合](remoteapp-create-hybrid-deployment.md)主题中。
6. 决定你是否要将集合加入到你的域。 如果是，你需要使用 AD Connect 集成 Azure AD 和 Active Directory 环境。 下面的**步骤 2** 中介绍了这部分内容。
7. 单击“**创建 RemoteApp 集合**”。

## <a name="step-2-configure-active-directory-directory-synchronization-optional"></a>步骤 2：配置 Active Directory 目录同步（可选）
如果你想要使用 Active Directory，则必须在 Azure Active Directory 和本地 Active Directory 之间进行目录同步，Azure RemoteApp 才可以将用户、联系人和密码同步到你的 Azure Active Directory 租户。 有关计划信息，请参阅[为 Azure RemoteApp 配置 Active Directory](remoteapp-ad.md)。 此外，你也可以直接转到 [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) 了解相关信息。

## <a name="step-3-publish-apps"></a>步骤 3：发布应用
Azure RemoteApp 应用是你为用户提供的应用或程序。 它位于你为该集合上载的模板映像中。 当用户访问应用时，该应用就像是在用户的本地环境中运行一样，但实际上它是在 Azure 中的虚拟机中运行。 

你需要先发布这些应用，用户才可以访问它们 - 发布应用可让你的用户通过远程桌面客户端访问应用。

你可以将多个应用发布到你的 Azure RemoteApp 集合。 在发布页面中，单击“**发布**”来添加程序。 你可以从模板映像的“**开始**”菜单进行发布，也可以通过在模板映像上为应用指定路径进行发布。 如果你选择从“**开始**”菜单添加，则选择要发布的应用。 如果你选择提供应用的路径，请提供应用的名称，以及应用在模板映像上的安装路径。

## <a name="step-4-configure-user-access"></a>步骤 4：配置用户访问权限
至此，你已创建了集合，接下来需要添加能够使用你的远程资源的用户。 如果使用 Active Directory，则你提供访问权限的用户需要存在于 Active Directory 租户中，此租户与你用来创建此集合的订阅相关联。

1. 在“快速入门”页上，单击“**配置用户访问权限**”。 
2. 输入工作帐户（来自 Active Directory）或想要授予访问权限的 Microsoft 帐户。
   
   **说明：** 
   
   确保你使用的是 “user@domain.com” 格式。
   
   如果你在集合中使用 Office 365 ProPlus，则必须为你的用户使用 Active Directory 标识。 这有助于验证授权。 
3. 对用户进行验证后，请单击“**保存**”。

## <a name="next-steps"></a>后续步骤
至此，你已成功创建并部署了 Azure RemoteApp 云集合。 下一步是让您的用户下载并安装远程桌面客户端。 你可以在 Azure RemoteApp 的“快速入门”页上找到客户端的 URL。 然后，让用户登录到客户端并访问你发布的应用。

### <a name="help-us-help-you"></a>请帮助我们改进，以便为你提供更好的帮助和支持
是否知道除了给这篇文章评级和在下面发表评论，还可以对文章本身进行更改？ 缺了什么？ 出现了错误？ 是否编写了令人困惑的内容？ 向上滚动并单击“在 GitHub 上编辑”  进行更改 - 更改的内容会发送给我们进行审阅，一旦签核，便可以在此处看到这些更改和改进。




<!--HONumber=Nov16_HO3-->


