---
title: "Azure RemoteApp 常见问题解答 | Microsoft Docs"
description: "了解有关 Azure RemoteApp 的常见问题解答。"
services: remoteapp
documentationcenter: 
author: lizap
manager: swadhwa
editor: 
ms.assetid: bad66603-91f9-437f-8a70-236405d2a27f
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f3a2927164a01dd4b76e264cd1b7152f170f79cb


---
# <a name="azure-remoteapp-faq"></a>Azure RemoteApp 常见问题解答
> [!IMPORTANT]
> Azure RemoteApp 将要停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

关于 Azure RemoteApp，听到以下问题。 有其他问题？ 请访问 [RemoteApp 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) ，提出需要了解的内容，或在下面发表评论。

## <a name="cant-find-what-youre-looking-for-have-a-question-we-didnt-answer"></a>找不到要查找的内容？ 有我们没有回答的问题？
如果找不到所需的信息或者有此处未涵盖的其他问题，请转到 [Azure RemoteApp 论坛](http://aka.ms/araforum) ，在那里询问问题。 我们会随时在此处添加更多回答。

## <a name="what-is-azure-remoteapp"></a>什么是 Azure RemoteApp？
* **什么是 Azure RemoteApp？**  RemoteApp 是一项 Azure 服务，借助它可以从多个不同的用户设备对应用程序进行安全的远程访问。 详细了解 [Azure RemoteApp](remoteapp-whatis.md)。
* **有哪些部署选项？**  有两种类型的 RemoteApp 集合：云和混合。 需要哪一种取决于多种因素，例如是否需要加入域。 在 [此处](remoteapp-collections.md)讨论了所有这些决策。

## <a name="quick-tips-on-using-azure-remoteapp"></a>有关使用 Azure RemoteApp 的快速提示
* **能够连接多长时间？在启动之前，可以空闲多长时间？** 4 小时。 如果你或者你的其中一个用户空闲了 4 小时，将自动从 Azure RemoteApp 中注销。 请查看 [Azure 订阅和服务限制、配额与约束](../azure-subscription-service-limits.md)中的其他默认设置。
* **能否免费试用此服务？**  是的。 有可用 30 天的免费试用版。 试用结束后，可以转换为付费帐户（可在产品中使用）或停止使用该服务。 转到 [portal.azure.com](http://portal.azure.com) 创建 RemoteApp 的新实例即可开始免费试用。 使用免费试用版，可以构建 RemoteApp 的 2 个实例，每个实例可以有 10 个用户。 请记住，此试用版仅 30 天 有效。
  
  ## <a name="azure-remoteapp-subscription-details"></a>Azure RemoteApp 订阅详细信息
* **有哪些服务限制？** 可以在 [Azure 订阅和服务限制、配额与约束](。。/azure-subscription-service-limits。md)。 如果有更多问题，请告知。
* **必须要有多少用户？**  至少要有 20 个用户。 为清楚起见再重复一遍 - 最小值是 20。 需要为 20 个用户付费。 
* **RemoteApp 的费用是多少？** 请查看 [Azure RemoteApp 定价详细信息 ](https://azure。microsoft。com/pricing/details/remoteapp/)。
* **一种类型的集合费用会高于另一种吗？**  是的，它会，具体取决于集合需求。 混合集合需要从 Azure RemoteApp 连接到本地网络。 如果使用现有的 VNET/快速路由，则无需额外付费。 但如果使用新的 Azure VNET 和网关或快速路由，则需要支付 [VPN 网关](https://azure.microsoft.com/pricing/details/vpn-gateway)或[快速路由](https://azure.microsoft.com/pricing/details/expressroute/)费用。 此费用（链接中有详细说明）在每月的 Azure RemoteApp 费用的基础上。

## <a name="collections-whats-supported-which-should-you-use-and-others"></a>集合 - 支持的操作、应使用的功能及其他
* **是否支持自定义的业务线 (LOB) 应用程序？**  是的。 若要在 Azure RemoteApp 中使用自定义应用程序，请创建 [自定义模板映像](remoteapp-create-custom-image.md)，然后将其上载到 RemoteApp 集合。
* **自定义的 LOB 应用程序是否会在 Azure RemoteApp 中正常工作？**  确定此问题的最好方法就是对其进行测试。 请查看 [RD 兼容中心](http://www.rdcompatibility.com/compatibility/default.aspx)。
* **哪种部署方法（云或混合）最适合组织？**  如果想与单一登录 (SSO) 完全集成并实现安全的本地网络连接，混合集合能够提供最完整的体验。 云集合使用多个身份验证方法提供一种灵活简单的方式来确定部署问题。 详细了解 [部署选项](remoteapp-whatis.md)。
* **我们在本地或 Azure 中设置了 SQL 或其他数据库。应使用哪种部署类型？** 这取决于 SQL 或后端数据库的位置。 如果数据库在专用网络中，请使用混合集合。 如果数据库向 Internet 公开，并允许客户端连接到它，则可以使用云集合。
* **驱动器映射、USB 和串行端口、剪贴板共享以及打印机重定向功能怎么样？**  Azure RemoteApp 支持所有这些功能。 默认情况下启用剪贴板共享和打印机重定向。 可以在 [此处](remoteapp-redirection.md)了解更多有关重定向的信息。 

## <a name="template-images"></a>模板映像
* **能否使用云或现有的虚拟机作为 RemoteApp 集合的模板？**  能！ 可以基于 Azure VM 创建映像，使用订阅附带的映像之一，或创建自定义映像。 请查看 [RemoteApp 映像选项](remoteapp-imageoptions.md)。

## <a name="network-options"></a>网络选项
* **混合集合需要使用 VNET。能否使用现有的 VNET？** 如果现有的 VNET 是 Azure VNET，则可以。 有关详细信息，请参阅[混合集合说明](remoteapp-create-hybrid-deployment.md)中的“步骤 1：设置虚拟网络”。
* **能否在云集合中使用 VNET？**  当然可以。 有关详细信息，请查看 [创建云集合](remoteapp-create-cloud-deployment.md)，尤其是步骤 1。

## <a name="authentication-options"></a>身份验证选项
* **如何进行身份验证？支持哪些方法？** 云集合支持 Microsoft 帐户和 Azure Active Directory 帐户，后者也是 Office 365 帐户。 混合集合仅支持已（使用 [Azure Active Directory 同步](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)等工具）与 Windows Server Active Directory 部署同步的 Azure Active Directory 帐户；具体而言，已与密码同步选项同步或已与配置的 Active Directory 联合身份验证服务 (AD FS) 联盟同步。 还可以配置[多重身份验证 (MFA)](https://azure.microsoft.com/services/multi-factor-authentication/)。

> [!NOTE]
> Azure Active Directory 用户必须来自与订阅关联的租户。 （可以在门户的“**设置**”选项卡上查看和修改订阅。 有关详细信息，请参阅[更改 RemoteApp 使用的 Azure Active Directory 租户](remoteapp-changetenant.md)。）
> 
> 

* **为什么无法为 Azure Active Directory 帐户指定访问权限？**  Azure Active Directory 用户必须来自与订阅关联的目录。 可以在门户的“设置”选项卡上查看或修改该目录。 有关详细信息，请参阅 [更改 RemoteApp 使用的 Azure Active Directory 租户](remoteapp-changetenant.md) 。

## <a name="clients-what-device-can-i-use-to-access-azure-remoteapp"></a>客户端 - 可以使用哪种设备访问 Azure RemoteApp？
可以在 [在 Azure RemoteApp 中访问应用](remoteapp-clients.md)中找到想要的客户端信息，包括安装不同客户端的步骤。

* **客户端应用程序支持哪些设备和操作系统？**
   首先，计算机和平板电脑： 
  
  * Windows 10（客户端预览版）
  * Windows 8.1 和 Windows 8
  * Windows 7 Service Pack 1
  * Mac OS X
  * Windows RT
  * Android 平板电脑
  * iPad 和手机：
  * iPhone
  * Android 手机
  * Windows Phone
    
    [下载](https://www.remoteapp.windowsazure.com/Client下载/AllClients.aspx) RemoteApp 客户端。
* **Azure RemoteApp 是否支持瘦客户端？**  是，支持以下 Windows 嵌入式瘦客户端：
  
  * Windows Embedded Standard 7
  * Windows Embedded 8 Standard
  * Windows Embedded 8.1 Industry Pro
  * Windows 10 IoT Enterprise
* **远程桌面会话主机 (RDSH) 支持哪个版本的 Windows Server？**  Windows Server 2012 R2。

## <a name="support-and-feedback"></a>支持和反馈
* **什么是 RemoteApp 支持计划？**  免费提供计费和订阅管理支持。 可通过 [Azure 服务计划](https://azure.microsoft.com/support/plans/)获取技术支持。 还可以通过 [Azure 论坛](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp)获取免费的社区支持。 
* **如何提交反馈？** 请访问 [反馈论坛](https://feedback。azure。com/forums/247748-azure-remoteapp/)。
* **如果想要详细了解 Azure RemoteApp，可以与谁联系？** 除了[论坛](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp)（是提出问题的好地方）外，还可以加入每周的[询问专家在线研讨会](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)（可以在其中谈论 RemoteApp 的所有内容）。
* **RemoteApp 文档怎么样？**  很高兴听到这个问题。 除了门户帮助抽屉（只需在门户的任何页上单击 **？** ）中的帮助内容外，以下文章可帮助用户了解有关 RemoteApp 的所有信息：
  
  * **入门：**
    * [什么是 RemoteApp？](remoteapp-whatis.md)
    * [RemoteApp 模板映像中有哪些内容？](remoteapp-images.md)
    * [许可如何工作？](remoteapp-licensing.md)
    * [RemoteApp 和 Office 如何协同工作？](remoteapp-o365.md)
    * [重定向在 RemoteApp 中如何工作？](remoteapp-redirection.md)
  * **部署：**
    * [创建自定义模板映像](remoteapp-create-custom-image.md)
    * [创建混合集合](remoteapp-create-hybrid-deployment.md)
    * [创建云集合](remoteapp-create-cloud-deployment.md)
    * [为 RemoteApp 配置 Azure Active Directory](remoteapp-ad.md)
    * [在 RemoteApp 中发布应用](remoteapp-publish.md)
  * **管理：**
    
    * [添加用户](remoteapp-user.md)
    * [配置和使用 RemoteApp 的最佳做法](remoteapp-bestpractices.md)    
    
    视频！ 我们还提供了大量有关 RemoteApp 的视频。 一些视频提供了简介（[Azure RemoteApp 简介](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)），而其他视频介绍了部署（[云部署](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be)和[混合部署](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)）。 请查看！

### <a name="help-us-help-you"></a>请帮助我们改进，以便为用户提供更好的帮助和支持
是否知道除了给这篇文章评级和在下面发表评论，还可以对文章本身进行更改？ 缺了什么？ 出现了错误？ 是否编写了令人困惑的内容？ 向上滚动并单击“在 GitHub 上编辑”  进行更改 - 更改的内容会发送给我们进行审阅，一旦签核，便可以在此处看到这些更改和改进。




<!--HONumber=Nov16_HO2-->


