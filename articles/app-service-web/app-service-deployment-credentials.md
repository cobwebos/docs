---
title: "Azure 应用服务部署凭据 | Microsoft Docs"
description: "了解如何使用 Azure 应用服务部署凭据。"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: wpickett
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/21/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 73753bb96d2c9680ed5d5bdf5eb3780e371c08d3
ms.openlocfilehash: 53f971624c2c7a7f64eed257aeb0c402461cc7ba


---
# <a name="azure-app-service-deployment-credentials"></a>Azure 应用服务部署凭据
[Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)平台支持两种类型的内容部署凭据。
* 部署凭据：用户范围凭据
* 发布配置文件：应用范围凭据 

## <a name="a-nameuserscopeauser-scoped-credentials"></a><a name="userscope"></a>用户范围凭据
用户范围凭据由 Azure 用户创建，并直接映射到 Microsoft 帐户（而非任何特定的应用服务应用）。 如果 Azure 门户中的每个应用服务应用在“应用部署”>“部署凭据”下都有一个编辑入口点，则可在 [Azure 门户](https://portal.azure.com)中设置或重置用户范围部署凭据。 无需考虑入口点，对这些用户范围凭据的编辑将应用于整个 Microsoft 帐户。 这些凭据经常用于 FTP 和 Git 部署。

![Azure 应用服务部署凭据](./media/app-service-deployment-credentials/deployment_credentials.png)
 
通过基于角色的访问控制 (RBAC) 或共同管理员权限委派对 Azure 资源的访问权限时，每个接收访问权限的 Azure 用户都可以使用自己的用户范围凭证，直到撤消访问权限。 不应与其他 Azure 用户共享这些部署凭据。

## <a name="a-nameappscopeaapp-scoped-credentials"></a><a name="appscope"></a>应用范围凭据
应用范围凭据由应用服务平台自动创建。 它们存储在每个应用服务应用的 XML 发布配置文件中。 发布配置文件位于 [Azure 门户](https://portal.azure.com)中，可通过应用的“概述”边栏选项卡中的“获取发布配置文件”操作获取。 这些凭据常用于基于 WebDeploy 的部署。 还可以用于 FTP 或 Git 部署。 Visual Studio 是基于 WebDeploy 的部署的入口点，能分析身份验证的发布配置文件。

![Azure 应用服务发布配置文件](./media/app-service-deployment-credentials/publish_profile.png)

通过基于角色的访问控制 (RBAC) 或共同管理员权限委派对 Azure 资源的访问权限时，每个接收访问权限的 Azure 用户都可以下载相同的特定于应用的发布配置文件。 可随时从 Azure 门户应用“概述”边栏选项卡重置发布配置文件。 撤销委派访问后，最好重置应用范围凭据。



<!--HONumber=Nov16_HO4-->


