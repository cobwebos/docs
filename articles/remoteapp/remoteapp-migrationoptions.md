---
title: "有关迁移出 Azure RemoteApp 的选项 |Microsoft Docs"
description: "了解有关迁移出 Azure RemoteApp 的选项。"
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: c4e0e5bc-5c13-4487-b1b6-ebf2a5edc1f0
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 801eef31e4a06fd1b89ddf504d643d9dcfcdcbdc
ms.openlocfilehash: 99e6f4c1be6fa73db05fc4635fa5c34a579af588
ms.lasthandoff: 02/24/2017


---
# <a name="options-for-migrating-out-of-azure-remoteapp"></a>迁移出 Azure RemoteApp 的选项
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

如果由于[停用公告](https://go.microsoft.com/fwlink/?linkid=821148)或已经完成评估，从而停止使用 Azure RemoteApp，你需要将 Azure RemoteApp 迁移到另一个应用服务。 有两种迁移方法：自托管（通常称为基础结构即服务 [IaaS]）部署或完全托管（通常称为平台即服务或软件即服务 [PaaS/SaaS]）产品/服务。 

自助服务 IaaS 是一种由自己管理、操作和所有的自助式部署，直接部署在虚拟机 (VM) 或物理系统上。 而另一方面，完全托管式 PaaS/SaaS 产品/服务更像 Azure RemoteApp，即合作伙伴在远端解决方案之上提供一个服务层，处理操作并提供服务，而作为客户的你只做一些映像和应用管理的事情。

[查看有关迁移选项的 Azure RemoteApp 网络研讨会](https://social.msdn.microsoft.com/Forums/azure/40557aaa-3e9f-403c-b221-ad3eac10dc56/migration-option-webinar-recordings?forum=AzureRemoteApp)，或者继续阅读以获取详细信息（包括不同托管选项的示例）。

## <a name="self-managed-iaas-solutions"></a>自托管 (IaaS) 解决方案
### <a name="rds-on-iaas"></a>**RDS on IaaS**
你可以使用 RemoteApp 或本地或者托管环境（比如在 Azure 虚拟机上）中的台式计算机部署基于会话的原生远程桌面服务（在 Windows Server 中）。 对于已经熟悉 RDS 部署或已经具备 RDS 部署的技术专业知识的客户来说，RDS on IaaS 部署是最佳选择。 

> [!NOTE]
> 你需要批量许可软件保障 (SA) 来获得用于使用此部署选项的 RDS 客户端访问许可证。
> 
> 

部署和修补模板的使用使得在 Azure 虚拟机上部署 RDS 比以往更加容易（阅读[概述](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/)然后[获取](https://aka.ms/rdautomation)）。 尽管有更多的自定义设置和配置，通过使用[自动缩放脚本](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76)，你仍然可以在 Azure RemoteApp 中利用 Azure 经典部署模型资源（而非 Azure 资源模型资源）获得同样灵活的扩展能力。 在 Azure 虚拟机上部署 RDS 时，你可通过 [Azure 支持部门](https://azure.microsoft.com/support/plans/)获得支持，提供支持的仍然是为你提供 Azure RemoteApp 支持的那些专业人员。 通过联络 [Azure 支持部门](https://azure.microsoft.com/support/plans/)可基于现有使用情况获取估计成本，或者可以使用即将发布的成本计算器自己计算费用。  此外，通过 N 系列虚拟机（目前处于特邀预览阶段） 可以添加 vGPU，参加我们的 Ignite 大会，了解更多有关添加 vGPU 的信息以及如何[利用 Windows Server 2016 中的 RDS 改进](https://myignite.microsoft.com/videos/2794)。   

我们针对 [Windows Server 2012 R2](http://aka.ms/rdsonazure) 和 [Windows Server 2016](http://aka.ms/rdsonazure2016) 提供了逐步部署指南以帮助你进行部署。 查看[远程桌面博客](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services)了解最新新闻。

### <a name="citrix-on-iaas"></a>**Citrix on IaaS**
可以在本地或托管环境中（如在 Azure 虚拟机上）进行基于会话的 XenApp 或 XenDesktop 的原生 Citrix 部署。 

查看 [Citrix XA 7.6 on Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx) 的逐步部署指南以了解详细信息。 阅读更多有关 [Citrix on Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx) 的信息，包括价格计算器。 你还可以找到 [Citrix 联系人](http://citrix.com/English/contact/index.asp)一起讨论你的选项。

## <a name="fully-managed-paassaas-offerings"></a>完全托管式 (PaaS/SaaS) 产品/服务
### <a name="citrix-cloud"></a>**Citrix 云**
[Citrix 现有的云解决方案](https://www.citrix.com/products/citrix-cloud/)在体系结构上与 Citrix XenApp Express 相同。 Citrix 正在为现有的 Azure RemoteApp 客户提供 [50% 的折扣促销](https://www.citrix.com/blogs/2016/10/03/special-promotion-for-microsoft-azure-remoteapp-customers/)。 

### <a name="citrix-xenapp-express-in-tech-preview"></a>**Citrix XenApp Express（技术预览版）**
[只要注册便可获得技术预览版](http://now.citrix.com/remoteapp)并观看 [Ignite 大会](https://myignite.microsoft.com/videos/2792)（从 20:30 分开始）。 XenApp Express 在体系结构上与 Citrix 云相同，只不过它包括简化的管理 UI 和其他类似于 Azure RemoteApp 的特性与功能。 

了解有关 [Citrix XenApp Express](http://now.citrix.com/remoteapp) 的更多信息。   

### <a name="citrix-service-provider-program"></a>**Citrix 服务提供商计划**
Citrix 服务提供商计划使得服务提供商为中小型企业提供虚拟云计算变得简单，中小型企业可以通过一种简单且即付即用的模式获得想要的服务。 Citrix 服务提供商利用任意设备、无处不在的访问、最广泛的应用程序支持、丰富的体验、增强的安全性和增加的可扩展性拓展自己的 Microsoft SPLA 业务并扩大其 RDS 平台投资。 相应地，Citrix 服务提供商也会吸引更多订户、提高客户满意度并降低运营成本。 [了解更多信息](http://www.citrix.com/products/service-providers.html)或[查找合作伙伴](https://www.citrix.com/buy/partnerlocator.html)。

### <a name="frame"></a>Frame

企业、政府、托管服务提供商和领先的软件供应商中的 IT 组织选择 Frame 创建和管理其在云中通过软件定义的安全工作区。 不管是小型组织还是大型组织，都可以使用 Frame 无比轻松地让用户在任何设备的任何浏览器上访问 Windows 应用程序。 Frame 平台提供管理员从云部署应用程序所需的一切，包括 Azure 基础结构和 RDS 许可证（用户可以自带 Azure 帐户和许可证）。 

了解关于 [Azure 中 Frame](https://www.fra.me/ara) 的详细信息。 

主要地点：美国加州圣马特奥市

运营区域：全球

Microsoft 合作伙伴：是

1-480-269-4668


### <a name="microsoft-hosted-service-provider"></a>**Microsoft 托管服务提供商**
托管服务合作伙伴通常提供完全托管式 Windows 桌面和应用程序服务，可能包括使用合作伙伴与 Microsoft 和其他软件提供商签署的许可协议，以及为了允许转售订户访问许可证 (SAL) 必须具有的服务提供商许可协议来管理 Azure 资源、操作系统、应用程序和服务台。 下面提供了详细信息以及一些专门帮助客户迁移 Azure RemoteApp 的托管服务提供商的联系信息。 查看[当前的托管服务提供商列表](http://aka.ms/rdsonazurecertified) ，这些提供商已经完成 RDS on IaaS 的学习路径和评估。  

#### <a name="aspex"></a>**ASPEX**
[ASPEX](http://www.aspex.be/en) 专注于正在向云计算转型的 ISV 以及希望优化其当前云设置的 ISV。 ASPEX 提供广泛的托管服务、开发运营以及咨询服务。  

主要地点：比利时安特卫普

运营区域：西欧

合作伙伴状态：[银牌](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)

Microsoft 云服务提供商：是

提供基于会话的 RemoteApp 和桌面解决方案：是，两者

Azure RemoteApp 迁移解决方案：是，[了解更多信息](https://www.aspex.be/en/azure-remote-apps)

**联系人：**

* 电话：+3232202198
* 邮件：[info@aspex.be](mailto:info@aspex.be)
* 网址：[http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>**Conexlink（平台名称：MyCloudIT）**
[MyCloudIT](https://mycloudit.com) 是供 IT 公司使用的一个自动化平台，可以简化、优化和扩展迁移，并在 Microsoft Azure 云中提供远程桌面、远程应用程序和基础结构。 

只需点击几下，MyCloudIT 平台即可减少 95% 的部署时间、降低 30% 的 Azure 部署成本，并将其客户的整个 IT 基础结构迁移到云中。 合作伙伴现在能够以前所未有的方式管理单个全局仪表板中的客户以及世界各地的服务终端用户，并在无需增加额外开销或进行大量 Azure 培训的情况下即可提高收入。  

主要地点：美国德克萨斯州达拉斯

运营区域：全球

合作伙伴状态：[金牌](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)

Microsoft 云服务提供商：是

提供基于会话的 RemoteApp 和桌面解决方案：是，两者

Azure RemoteApp 迁移解决方案：是，[了解更多信息](https://mycloudit.com/remote-app-microsoft/)

**联系人：**

* 业务开发副总裁 Brian Garoutte
  
   电话：972-218-0741
  
   电子邮件： [brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>**Acuutech**
[Acuutech](http://www.acuutech.com) 专门提供托管桌面解决方案，从 Azure 和其自己的数据中心为全球客户群提供以 Microsoft 技术为依托的完整桌面和 ISV 应用程序体验。

主要地点：英国伦敦；新加坡；德克萨斯州休斯顿

运营区域：全球

合作伙伴状态：金牌

Microsoft 云服务提供商：是

提供基于会话的 RemoteApp 和桌面解决方案：是，两者

Azure RemoteApp 迁移解决方案：是，[了解更多信息](http://www.acuutech.com/ara-migration/)

**联系人：**

* 英国：
  
  5/6 York House, Langston Road,
  
  Loughton, Essex IG10 3TQ
  
  电话：+44 (0) 20 8502 2155
* 新加坡：
  
  100 Cecil Street, #09-02, 
  
  The Globe, Singapore 069532
  
  电话：+65 6709 4933
* 北美地区： 
  
  3601 S.Sandman St.
  
  Suite 200, Houston, TX 77098
  
  电话：+1 713 691 0800

#### <a name="saasplaza"></a>**SaaSplaza**
[SaaSplaza](http://www.saasplaza.com/) 提供完整的 Microsoft Dynamics 组合（NAV、AX、GP、SL、CRM）私有云和公有云 (Azure)。

主要位置：荷兰

运营区域：全球

合作伙伴状态：[金牌](https://partnercenter.microsoft.com/pcv/solution-providers/saasplaza_4295495801/791011_2?k=saasplaza)

Microsoft 云服务提供商：是

提供基于会话的 RemoteApp 和桌面解决方案：是，两者

**联系人：**

- 欧洲、非洲和中东：

   Prins Mauritslaan 29-35

   71 LP Badhoevedorp

   荷兰

   电话：+31 20 547 8060 

- 美洲：

   171 Saxony Road, Suite 105

   Encinitas, CA 92024

   San Diego

   美国

   电话：+1 858 385 8900 

- APAC：

   105 Cecil Street
   
   \#11-08, The Octagon

   Singapore 069534

   新加坡
   
   电话 - 新加坡：+65 6222 6591

   电话 - 澳大利亚：+61 2 8310 5568 
   
   电话 - 新西兰：+64 4 488 0321

## <a name="need-more-help"></a>需要更多帮助？
仍需要帮助你做出选择，或有更多疑问？ 使用以下其中一种方法获取帮助。 

1. 发送电子邮件至 [arainfo@microsoft.com](mailto:arainfo@microsoft.com)。
2. 联络 [Azure 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 首先提交 [Azure 支持请求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
3. 请拨打我们的电话。 [查找当地的销售电话号码](https://azure.microsoft.com/overview/sales-number/)。


