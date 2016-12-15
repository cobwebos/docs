---
title: "使用 Azure AD 应用程序代理连接器 | Microsoft Docs"
description: "介绍如何在 Azure AD 应用程序代理中创建和管理连接器组。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 5404372d-3092-4054-aeee-26afb1399f33
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ba87b73be45cb0893f418453bc0efb3293772c95


---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups---public-preview"></a>使用连接器组在单独的网络和位置上发布应用程序 - 公共预览版
> [!div class="op_single_selector"]
> * [Azure 门户](active-directory-application-proxy-connectors-azure-portal.md)
> * [Azure 经典门户](active-directory-application-proxy-connectors.md)
> 
> 

连接器组可用于各种方案，包括：

* 带有多个互联数据中心的站点。 在此情况下，需要在数据中心内保留尽可能多的流量，因为跨数据中心链接昂贵而缓慢。 可在每个数据中心中部署连接器，仅服务驻留在数据中心内的应用程序。 此方法可最大程度减少跨数据中心链接，并向用户提供完全透明的体验。
* 管理安装在不属于主要公司网络的隔离网络中的应用程序。 可使用连接器组在隔离网络上安装专用连接器，以便同样隔离到网络的应用程序。
* 对于在 IaaS 上安装用于云访问的应用程序，连接器组提供常见服务，保护对应用的所有访问。 连接器不在公司网络上创建其他依赖项或分裂应用体验。 连接器可安装在每个云数据中心中，并且仅服务驻留在此网络中的应用程序。 可安装多个连接器，实现高可用性。
* 对多林环境的支持，在该环境中可为每个林部署特定连接器，并将其设置为服务特定应用程序。
* 连接器组可在灾难恢复站点中用于检测故障转移或用作主站点的备份。
* 连接器组还可用于服务单个租户中的多个公司。

## <a name="prerequisite-create-your-connectors"></a>先决条件：创建连接器
为了为连接器分组，必须先确保[已安装多个连接器](active-directory-application-proxy-enable.md)。 安装新的连接器时，该连接器会自动加入**默认**连接器组。

## <a name="step-1-create-connector-groups"></a>步骤 1：创建连接器组
可以创建任意数量的连接器组。 连接器组的创建在 [Azure 门户](https://portal.azure.com)中完成。

1. 选择“Azure Active Directory”，转到目录的管理仪表板。 在该处选择“企业应用程序” > “应用程序代理”。
2. 选择“连接器组”按钮。 此时会显示“新建连接器组”边栏选项卡。
3. 为新的连接器组提供一个名称，然后使用下拉菜单选择哪些连接器属于此组。
4. 当连接器组完成时，选择“保存”。

## <a name="step-2-assign-applications-to-your-connector-groups"></a>步骤 2：将应用程序分配到连接器组
最后一步是将每个应用程序设置到将要服务它的连接器组。

1. 在目录的管理仪表板中，选择“企业应用程序” > “所有应用程序”> 需要分配到连接器组的应用程序 >“应用程序代理”。
2. 在“连接器组”下，使用下拉菜单选择要让应用程序使用的组。
3. 单击“保存”应用所做的更改。

## <a name="see-also"></a>另请参阅
* [启用应用程序代理](active-directory-application-proxy-enable.md)
* [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)
* [启用条件性访问](active-directory-application-proxy-conditional-access.md)
* [解决使用应用程序代理时遇到的问题](active-directory-application-proxy-troubleshoot.md)

有关最新新闻和更新，请参阅 [应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Nov16_HO3-->


