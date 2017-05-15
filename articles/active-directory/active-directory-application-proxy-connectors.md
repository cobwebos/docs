---
title: "经典门户 Azure AD 应用代理连接器 | Microsoft Docs"
description: "介绍如何在 Azure AD 应用程序代理中创建和管理连接器组。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: b283796a-9679-4c79-b703-802bb850f65d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: d37f37f77a32f388fac4271af2a5ebda21c014eb
ms.contentlocale: zh-cn
ms.lasthandoff: 04/28/2017


---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>使用连接器组在单独的网络和位置上发布应用程序。
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
若要对连接器进行分组，请[安装多个连接器](active-directory-application-proxy-enable.md)，然后对其进行命名和分组。 最后必须将它们分配到特定应用。

## <a name="step-1-create-connector-groups"></a>步骤 1：创建连接器组
可以创建任意数量的连接器组。 连接器组的创建在 Azure 经典门户中完成。

1. 选择目录，然后单击“配置”。  
    ![应用程序代理、配置屏幕截图 - 单击“管理连接器组”](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)
2. 在“应用程序代理”下，单击“管理连接器组”并通过提供组名称创建连接器组。  
    ![应用程序代理连接器组屏幕截图 - 命名新组](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>步骤 2：将连接器分配到组
创建连接器组后，将连接器移动到相应的组。

1. 在“应用程序代理”下，单击“管理连接器”。
2. 在“组”下，为每个连接器选择所需的组。 连接器在新组中变为活动状态最多可能需要 10 分钟。  
    ![应用程序代理连接器屏幕截图 - 从下拉菜单中选择组](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>步骤 3：将应用程序分配到连接器组
最后一步是将每个应用程序设置到为其提供服务的连接器组。

1. 在 Azure 经典门户中，在你的目录中选择要分配到组的应用程序，然后单击“配置”。
2. 在“连接器组”下，选择要让应用程序使用的组。 此更改立即应用。  
    ![应用程序代理连接器组屏幕快照 - 从下拉菜单中选择组](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)

## <a name="see-also"></a>另请参阅
* [启用应用程序代理](active-directory-application-proxy-enable.md)
* [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)
* [启用条件性访问](active-directory-application-proxy-conditional-access.md)
* [解决使用应用程序代理时遇到的问题](active-directory-application-proxy-troubleshoot.md)

有关最新新闻和更新，请参阅 [应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)

