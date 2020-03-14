---
title: 通过 CloudSimple 访问 Azure VMware 解决方案-门户
description: 介绍如何在 Azure 门户中访问 VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d27bab243f6805436465f5027e519d33e20f6f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244727"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>通过 CloudSimple 中的门户访问 VMware 解决方案 Azure 门户

支持单一登录以访问 CloudSimple 门户。 登录到 Azure 门户后，可以访问 CloudSimple 门户，而无需再次登录。 首次访问 CloudSimple 门户时，系统将提示你授权[CloudSimple Service 授权](#consent-to-cloudsimple-service-authorization-application)应用程序。  授权是一次性的操作。

## <a name="before-you-begin"></a>开始之前

具有内置**所有者**和**参与者**角色的用户可以访问 CloudSimple 门户。  必须在部署 CloudSimple 服务的资源组上配置角色。  还可以在 CloudSimple 服务对象上配置角色。  有关检查角色的详细信息，请参阅[查看角色分配](https://docs.microsoft.com/azure/role-based-access-control/check-access)一文。 只有具有内置**所有者**和**参与者**角色的用户才能访问 CloudSimple 门户。  必须在订阅上配置角色。  有关检查角色的详细信息，请参阅[查看角色分配](https://docs.microsoft.com/azure/role-based-access-control/check-access)一文。

如果你使用的是自定义角色，则角色应在 ```Actions```下执行以下任何操作。  有关自定义角色的详细信息，请参阅[Azure 资源的自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。  如果任何操作是 ```NotActions```的一部分，则用户无法访问 CloudSimple 门户。

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

1. 选择“所有服务”。

2. 搜索 " **CloudSimple Services**"。

3. 选择要在其上创建私有云的 CloudSimple 服务。

4. 在 "**概述**" 页上，单击 **"前往 CloudSimple 门户"** 。  如果你是第一次从 Azure 门户访问 CloudSimple 门户，则系统会提示你授权[CloudSimple Service 授权](#consent-to-cloudsimple-service-authorization-application)应用程序。 

    ![启动 CloudSimple 门户](media/launch-cloudsimple-portal.png)

> [!NOTE]
> 如果直接从 Azure 门户中选择私有云操作（如创建或扩展私有云），CloudSimple 门户会打开到指定的页面。

在 CloudSimple 门户中，选择侧边菜单上的 "**主页**" 以显示有关私有云的摘要信息。 显示私有云的资源和容量以及需要注意的警报和任务。 对于常见任务，请单击页面顶部的命名图标。

![主页](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>同意 CloudSimple Service Authorization 应用程序

首次从 Azure 门户启动 CloudSimple 门户要求你同意 CloudSimple Service 授权应用程序。  选择 "**接受**" 以授予请求的权限并访问 CloudSimple 门户。

![同意 CloudSimple 服务授权-管理员](media/cloudsimple-azure-consent.png)

如果你具有全局管理员权限，则可以同意你的组织。  **代表你的组织选择 "同意"** 。

![同意 CloudSimple 服务授权-全局管理员](media/cloudsimple-azure-consent-global-admin.png)

如果你的权限不允许访问 CloudSimple 门户，请联系你的租户的全局管理员以授予所需的权限。  全局管理员可以代表您的组织同意。

![同意 CloudSimple 服务授权-需要管理员](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[创建私有云](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)
