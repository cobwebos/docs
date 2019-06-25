---
title: CloudSimple 服务启用 Azure VMware 解决方案
description: 介绍如何启用 Azure 订阅上的 CloudSimple 服务，然后注册 CLoudSimple 资源提供程序
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154858"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>注册你的 Azure 订阅上的 Microsoft.VMwareCloudSimple 资源提供程序

CloudSimple 服务，可使用 Azure CloudSimple VMware 解决方案。 可以将 Microsoft.VMwareCloudSimple 服务注册为资源提供程序。

## <a name="register-the-resource-provider"></a>注册资源提供程序

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“所有服务”  。

3. 搜索并选择**订阅**。

    ![选择订阅](media/cloudsimple-service-select-subscriptions.png)

4. 选择你想要启用 CloudSimple 服务的订阅。

5. 单击**资源提供程序**的订阅。

6. 使用**Microsoft.VMwareCloudSimple**以筛选资源提供程序。

7. 选择资源提供程序，然后单击**注册**。

    ![注册资源提供程序](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[创建 CloudSimple 服务](create-cloudsimple-service.md)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)
