---
title: 访问 Azure 的 VMware 解决方案通过 CloudSimple-门户
description: 介绍了如何从 Azure 门户的 CloudSimple 门户访问 VMware 解决方案
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d6f44005080bbd8583ae2e2fdad31ef2c823c7ca
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154519"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>通过从 Azure 门户的 CloudSimple 门户访问 VMware 解决方案

单一登录支持 CloudSimple 门户的访问权限。 登录到 Azure 门户后，您可以访问 CloudSimple 门户，而无需再次登录。 首次访问 CloudSimple 门户，系统会提示你授权[CloudSimple 服务授权](#consent-to-cloudsimple-service-authorization-application)应用程序。  授权是一次性操作。

## <a name="before-you-begin"></a>开始之前

只有具有内置的用户**所有者**并**参与者**角色可以访问 CloudSimple 门户。  必须在订阅上配置角色。  检查你的角色的详细信息，请参阅[查看角色分配](https://docs.microsoft.com/azure/role-based-access-control/check-access)一文。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

1. 选择“所有服务”  。

2. 搜索**CloudSimple 服务**。

3. 选择你想要创建您的私有云的 CloudSimple 服务。

4. 上**概述**页上，单击**转到 CloudSimple 门户**。  如果首次从 Azure 门户中访问 CloudSimple 门户，系统会提示授权[CloudSimple 服务授权](#consent-to-cloudsimple-service-authorization-application)应用程序。 

    ![启动 CloudSimple 门户](media/launch-cloudsimple-portal.png)

> [!TIP]
> 如果直接从 Azure 门户选择私有云的操作 （如创建或扩展私有云），CloudSimple 门户可打开所指示的页。

在 CloudSimple 门户中，选择**主页**以显示有关私有云的摘要信息的边栏菜单上。 以及警报和需要注意的任务显示的资源和私有云容量。 常见的任务，请单击页面顶部的命名的图标。

![主页](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>同意 CloudSimple 服务授权应用程序

第一次启动 CloudSimple 门户从 Azure 门户 CloudSimple 服务授权应用程序需要您的同意。  选择**接受**授予请求的权限以及访问 CloudSimple 门户。 

![同意 CloudSimple 服务授权的管理员](media/cloudsimple-azure-consent.png)

如果您具有全局管理员权限，您可以为你的组织进行许可。  选择**代表你的组织同意**。

![同意 CloudSimple 服务授权的全局管理员](media/cloudsimple-azure-consent-global-admin.png)

如果你的权限不允许对 CloudSimple 门户的访问，请联系你授予所需的权限的租户的全局管理员。  全局管理员可以代表你的组织同意。

![CloudSimple 服务授权同意-要求管理员](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[创建私有云](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)
