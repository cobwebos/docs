---
title: 访问 Azure VMware 解决方案（AVS）-门户
description: 描述如何从 Azure 门户访问 Azure VMware 解决方案（AVS）
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015944"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>从 Azure 门户访问 Azure VMware 解决方案（AVS）

支持使用单一登录访问 AVS 门户。 登录到 Azure 门户后，你可以访问 AVS 门户，而无需再次登录。 首次访问 AVS 门户时，系统会提示你授权[Avs 服务授权](#consent-to-avs-service-authorization-application)应用程序。 授权是一次性的操作。

## <a name="before-you-begin"></a>开始之前

具有内置**所有者**和**参与者**角色的用户可以访问 AVS 门户。 必须在部署了 AVS 服务的资源组上配置角色。 还可以在 AVS 服务对象上配置角色。 有关检查角色的详细信息，请参阅[查看角色分配](https://docs.microsoft.com/azure/role-based-access-control/check-access)一文。 只有具有内置**所有者**和**参与者**角色的用户才能访问 AVS 门户。 必须在订阅上配置角色。 有关检查角色的详细信息，请参阅[查看角色分配](https://docs.microsoft.com/azure/role-based-access-control/check-access)一文。

如果你使用的是自定义角色，则角色应在 ```Actions```下执行以下任何操作。  有关自定义角色的详细信息，请参阅[Azure 资源的自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。 如果任何操作是 ```NotActions```的一部分，则用户无法访问 AVS 门户。 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-avs-portal"></a>访问 AVS 门户

1. 选择“所有服务”。

2. 搜索**AVS 服务**。

3. 选择要在其上创建私有云的 AVS 服务。

4. 在 "**概述**" 页上，单击 **"前往 AVS" 门户**。 如果是第一次从 Azure 门户访问 AVS 门户，则系统会提示你授权[Avs 服务授权](#consent-to-avs-service-authorization-application)应用程序。 

    ![启动 AVS 门户](media/launch-cloudsimple-portal.png)

> [!NOTE]
> 如果直接从 Azure 门户中选择私有云操作（如创建或扩展私有云），AVS 门户会打开到指定的页面。

在 AVS 门户中，选择侧边菜单上的 "**主页**" 以显示有关 AVS 私有云的摘要信息。 显示您的 AVS 私有云的资源和容量以及需要注意的警报和任务。 对于常见任务，请单击页面顶部的命名图标。

![主页](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>同意 AVS 服务授权应用程序

首次从 Azure 门户启动 AVS 门户要求你同意 AVS 服务授权应用程序。 选择 "**接受**" 以授予请求的权限并访问 AVS 门户。

![同意 AVS 服务授权-管理员](media/cloudsimple-azure-consent.png)

如果你具有全局管理员权限，则可以同意你的组织。 **代表你的组织选择 "同意"** 。

![同意 AVS 服务授权-全局管理员](media/cloudsimple-azure-consent-global-admin.png)

如果你的权限不允许访问 AVS 门户，请联系你的租户的全局管理员以授予所需的权限。 全局管理员可以代表您的组织同意。

![同意 AVS 服务授权-需要管理员](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[创建私有云](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)
