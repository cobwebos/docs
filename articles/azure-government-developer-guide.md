---
title: "Azure Government 开发人员指南"
description: "这里提供了针对 Azure Government 的应用程序开发使用到的功能和指南对比"
services: 
cloud: gov
documentationcenter: 
author: Joharve2
manager: Chrisnie
editor: 
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/29/2015
ms.author: jharve
translationtype: Human Translation
ms.sourcegitcommit: 08de3cb0d62e800db88238764e4f9f221d2add06
ms.openlocfilehash: 42c1b022a22cefae15d32690856bdb80b0eaffa9


---
# <a name="microsoft-azure-government-developer-guide"></a>Microsoft Azure Government 开发人员指南
<p> Azure Government 环境是与 Microsoft 网络的其余部分隔离的物理实例。  此开发人员指南介绍应用程序开发人员和管理员可能需要进行交互并处理的 Azure 单独区域差异的详细信息。

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>本主题内容
* [概述](#Overview)
* [开发人员指南](#Guidance)
* [Microsoft Azure 政府版中的当前可用功能](#Features)
* [终结点映射](#Endpoint)
* [后续步骤](#next)

## <a name="a-nameoverviewaoverview"></a><a name="Overview"></a>概述
Microsoft Azure Government 是 Microsoft Azure 服务的单独实例，满足了美国联邦机构、州和地方政府，以及他们解决方案供应商的安全和合规需求。 Azure Government 提供来自非美国政府部署的物理和网络隔离，并提供筛选过的美国人员。

Microsoft 提供多种工具来创建和部署云应用程序到 Microsoft 的全局 Microsoft Azure 服务（简称“全局服务”）和 Microsoft Azure Government 服务。

当创建和部署应用程序到 Azure Government 服务，而不是全局服务时，开发人员需要了解这两个服务的主要区别。  特别是围绕设置和配置其编程环境、配置终结点、编写应用程序以及将它们作为服务部署到 Azure Government。

本文档中的信息总结了这些区别，并补充了 [Azure 政府版](http://www.azure.com/gov "Azure 政府版")站点和 MSDN 上 [Microsoft Azure 技术库](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN")的可用信息。 官方信息可能在其他许多位置也有提供，例如 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/ "Microsoft Azure Trust Center"/)、[Azure 文档中心](https://azure.microsoft.com/documentation/)和 [Azure 博客](https://azure.microsoft.com/blog/ "Azure Blogs"/)。

此内容适用于部署到 Microsoft Azure Government 的合作伙伴和开发人员。

## <a name="a-nameguidanceaguidance-for-developers"></a><a name="Guidance"></a>开发人员指南
由于大部分目前可用的技术内容假定应用程序正在为全局服务而开发，而不是 Microsoft Azure Government，务必要确保开发人员意识到开发应用程序并将其托管在 Azure Government 中的主要区别。

* 首先，存在服务和功能上的差异，这意味着全局服务的特定区域中的某些功能可能在 Azure Government 中不可用。
* 其次，Azure Government 中提供的功能，与全局服务的配置不同。  因此，应该检查您的示例代码、配置和步骤，以确保您是在 Azure Government 云服务环境中构建并执行。

## <a name="a-namefeaturesa-features-currently-available-in-microsoft-azure-government"></a><a name="Features"></a>Microsoft Azure 政府版中的当前可用功能
目前，Azure 政府版在 US GOV IOWA 和 US GOV VIRGINIA 区域提供以下服务：

* 虚拟机
* 虚拟机规模集
* 容器服务
* Batch 帐户
* 远程应用集合
* 可用性集
* 虚拟网络
* 负载均衡器
* 应用程序网关数
* 虚拟网络网关
* 本地网关
* 路由表
* 流量管理器配置文件
* ExpressRoute 线路
* 网络安全组
* 网络接口
* 公共 IP 地址
* 连接
* 存储帐户
* StorSimple 管理器
* 应用程序服务
* 媒体服务
* SQL 数据库
* SQL 数据仓库
* SQL Server Stretch Database
* Redis 缓存
* SQL 弹性池
* SQL Server
* Log Analytics
* 事件中心
* 服务总线命名空间
* Azure Active Directory
* 多重身份验证
* Rights Management
* 自动化帐户
* 应用商店

Azure 政府版还提供其他服务，并将继续添加更多服务。  有关服务的最新列表，请参阅[区域页](https://azure.microsoft.com/regions/#services)，其中突出显示了每个可用区域及其服务。

目前，US GOV IOWA 和 US GOV VIRGINIA 是支持 Azure 政府版的数据中心。  有关当前可用的数据中心和服务，请参阅上述“区域”页。

## <a name="a-nameendpointaendpoint-mapping"></a><a name="Endpoint"></a>终结点映射
当映射公共 Microsoft Azure 和 SQL 数据库终结点到 Azure 政府版特定终结点时，请参考下表。

| Name | Azure Government 终结点 |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId  | https://management.core.usgovcloudapi.net/ |
| GalleryUrl | https://gallery.usgovcloudapi.net/ |
| ManagementPortalUrl | https://manage.windowsazure.us |
| ServiceManagementUrl | https://management.core.usgovcloudapi.net/ |
| PublishSettingsFileUrl | https://manage.windowsazure.us/publishsettings/index |
| ResourceManagerUrl | https://management.usgovcloudapi.net/ |
| SqlDatabaseDnsSuffix | .database.usgovcloudapi.net |
| core.usgovcloudapi.net | core.usgovcloudapi.net |
| ActiveDirectoryAuthority | https://login-us.microsoftonline.com/ |
| GraphUrl | https://graph.windows.net/ |
| GraphEndpointResourceId | https://graph.windows.net/ |
| TrafficManagerDnsSuffix | usgovtrafficmanager.net |
| AzureKeyVaultDnsSuffix | vault.usgovcloudapi.net |
| AzureKeyVaultServiceEndpointResourceId | https://vault.usgovcloudapi.net |

* 若要通过 Azure AD 进行 Azure Resource Manager 身份验证，请参阅[对 Azure Resource Manager 请求进行身份验证](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="a-namenextanext-steps"></a><a name="next"></a>后续步骤
如果您想详细了解 Azure 政府版，请单击下面的一些链接。

* **[注册试用版](https://azuregov.microsoft.com/trial/azuregovtrial)**
* **[获取和访问 Azure 政府版](http://azure.com/gov)**
* **[Azure 政府版概述](/azure-government-overview)**
* **[Azure 政府版博客](http://blogs.msdn.com/b/azuregov/)**
* **[Azure 合规性](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md



<!--HONumber=Dec16_HO2-->


