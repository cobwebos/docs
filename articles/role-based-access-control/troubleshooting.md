---
title: 对 Azure 中的 RBAC 进行故障排除 | Microsoft Docs
description: 排查 Azure 基于角色的访问控制 (RBAC) 的问题。
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 557d3330ef155181c050a18b14d31b65ba1f2dcf
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295382"
---
# <a name="troubleshooting-rbac-in-azure"></a>对 Azure 中的 RBAC 进行故障排除

本文解答有关基于角色的访问控制 (RBAC) 的常见问题，以便你了解在 Azure 门户中使用角色时可能出现的情况，并可解决访问权限问题。 以下三种角色涵盖所有资源类型：

* 所有者  
* 参与者  
* 读取器  

所有者和参与者对管理体验具有完全访问权限，但是参与者无法向其他用户或组授予访问权限。 具有读者角色事情会变得更加有趣，因此，我们将着重介绍读者角色。 有关如何授予访问权限的信息，请参阅[使用 RBAC 和 Azure 门户管理访问权限](role-assignments-portal.md)。

## <a name="app-service"></a>应用服务
### <a name="write-access-capabilities"></a>写访问功能
如果为用户授予单个 Web 应用的只读访问权限，某些功能可能会被禁用，这可能不是你所期望的。 以下管理功能需要对 Web 应用具有**写**访问权限（参与者或所有者），并且在任何只读方案中不可用。

* 命令（例如启动、停止等。）
* 更改设置（如常规配置、缩放设置、备份设置和监视设置）
* 访问发布凭据和其他机密（如应用设置和连接字符串）
* 流式传输日志
* 诊断日志配置
* 控制台（命令提示符）
* 活动和最新部署（适用于本地 Git 连续部署）
* 估计费用
* Web 测试
* 虚拟网络（如果虚拟网络是以前具有写访问权限的用户所配置的，则仅对读者可见）。

如果无法访问以上任何磁贴，则需要让管理员提供对 Web 应用的“参与者”访问权限。

### <a name="dealing-with-related-resources"></a>处理相关资源
由于存在几个相互作用的不同资源，Web 应用程序是复杂的。 下面是包含几个网站的典型资源组：

![Web 应用程序资源组](./media/troubleshooting/website-resource-model.png)

因此，如果只授予某人对 Web 应用的访问权限，则 Azure 门户中的网站边栏选项卡上的很多功能将被禁用。

这些项需要对与网站对应的**应用服务计划**具有**写**访问权限：  

* 查看 Web 应用的定价层（“免费”或“标准”）  
* 规模配置（实例数、虚拟机大小、自动缩放设置）  
* 配额（存储、带宽、CPU）  

这些项需要对包含网站的整个**资源组**具有**写**访问权限：  

* SSL 证书和绑定（SSL 证书可以在同一资源组和地理位置中的站点之间共享）  
* 警报规则  
* 自动缩放设置  
* Application Insights 组件  
* Web 测试  

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](../azure-functions/functions-overview.md) 的某些功能需要写入权限。 例如，如果给用户分配读者角色，他们将无法查看函数应用中的函数。 门户将显示 (无访问权限)。

![函数应用无访问权限](./media/troubleshooting/functionapps-noaccess.png)

读者可单击“平台功能”选项卡，然后单击“所有设置”查看与函数应用（类似于 Web 应用）相关的一些设置，但无法修改任何这些设置。

## <a name="virtual-machine"></a>虚拟机
与 Web 应用程序很类似，虚拟机边栏选项卡上的某些功能需要对虚拟机或资源组中的其他资源具有写访问权限。

虚拟机与域名、虚拟网络、存储帐户和警报规则相关。

这些项需要对**虚拟机**具有**写**访问权限：

* 终结点  
* IP 地址  
* 磁盘  
* 扩展  

这些项需要对**虚拟机**和其所在的**资源组**（以及域名）具有**写**访问权限：  

* 可用性集  
* 负载均衡集  
* 警报规则  

如果无法访问以上任何磁贴，则需要让管理员提供对资源组的“参与者”访问权限。

## <a name="next-steps"></a>后续步骤
* [使用 RBAC 和 Azure 门户管理访问权限](role-assignments-portal.md)
* [查看活动日志以了解 RBAC 更改](change-history-report.md)

