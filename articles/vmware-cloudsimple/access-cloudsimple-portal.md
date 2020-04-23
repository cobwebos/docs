---
title: 通过云简单访问 Azure VMware 解决方案 - 门户
description: 介绍如何在 Azure 门户中访问 VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 91d3dd2eee6f771df23b72eae7a33122c42f1690
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869335"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>通过云简单门户从 Azure 门户访问 VMware 解决方案

访问云简单门户时，支持单一登录。 登录到 Azure 门户后，无需再次登录即可访问 CloudSimple 门户。 首次访问 CloudSimple 门户时，系统会提示您授权[云简单服务授权](#consent-to-cloudsimple-service-authorization-application)应用程序。  授权是一次性操作。

## <a name="before-you-begin"></a>开始之前

具有内置**所有者**和**参与者**角色的用户可以访问 CloudSimple 门户。  必须在部署 CloudSimple 服务的资源组上配置角色。  角色也可以在 CloudSimple 服务对象上配置。  有关检查角色的详细信息，请参阅[查看角色分配](https://docs.microsoft.com/azure/role-based-access-control/check-access)一文。 只有具有内置**所有者**和**参与者**角色的用户可以访问 CloudSimple 门户。  必须在订阅上配置角色。  有关检查角色的详细信息，请参阅[查看角色分配](https://docs.microsoft.com/azure/role-based-access-control/check-access)一文。

如果使用自定义角色，该角色应在 下```Actions```具有以下任何操作。  有关自定义角色的详细信息，请参阅 [Azure 资源的自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。  如果任何操作是 中的```NotActions```一部分，则用户无法访问 CloudSimple 门户。

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

1. 选择“所有服务”  。

2. 搜索**云简单服务**。

3. 选择要在其中创建私有云的云简单服务。

4. 在 **"概述"** 页上，单击"**转到云简单门户**"。  如果您是首次从 Azure 门户访问 CloudSimple 门户，系统将提示您授权[云简单服务授权](#consent-to-cloudsimple-service-authorization-application)应用程序。 

    ![启动云简单门户](media/launch-cloudsimple-portal.png)

> [!NOTE]
> 如果直接从 Azure 门户选择私有云操作（如创建或扩展私有云），CloudSimple 门户将打开到指示的页面。

在 CloudSimple 门户中，在侧菜单上选择 **"主页"** 以显示有关私有云的摘要信息。 将显示私有云的资源和容量，以及需要注意的警报和任务。 对于常见任务，请单击页面顶部的命名图标。

![主页](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>同意云简单服务授权应用程序

首次从 Azure 门户启动云简单门户需要获得云简单服务授权应用程序的同意。  选择 **"接受**"以授予请求的权限并访问云简单门户。

![同意云简单服务授权 - 管理员](media/cloudsimple-azure-consent.png)

如果您具有全局管理员权限，则可以同意您的组织。  选择“代表组织授予同意”****。

![同意云简单服务授权 - 全球管理员](media/cloudsimple-azure-consent-global-admin.png)

如果您的权限不允许访问 CloudSimple 门户，请与租户的全局管理员联系以授予所需的权限。  全局管理员可以代表您的组织同意。

![同意云简单服务授权 - 需要管理员](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[创建私有云](https://docs.microsoft.com/azure/vmware-cloudsimple/create-private-cloud/)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)
