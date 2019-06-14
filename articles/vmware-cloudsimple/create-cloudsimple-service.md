---
title: 创建 Azure VMware 解决方案通过 CloudSimple-服务
description: 介绍如何在 Azure 门户中创建 CloudSimple 服务
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fc08ce48b3b5a4b2cd28cb66e6dd4d9eae3f3885
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676948"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>创建 Azure 的 VMware 解决方案通过 CloudSimple-服务

若要开始使用 Azure 的 VMware 解决方案 CloudSimple，CloudSimple 服务在 Azure 门户中创建 Azure 的 VMware 解决方案。

> [!NOTE]
> 在创建 CloudSimple 服务之前，必须在 Azure 订阅上注册 Microsoft.VMwareCloudSimple 资源提供程序。 按照中的步骤[启用 Microsoft.VMwareCloudSimple 资源提供程序在 Azure 订阅上的](enable-cloudsimple-service.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-the-service"></a>创建服务

1. 选择“所有服务”  。

2. 搜索**CloudSimple 服务**。

    ![搜索 CloudSimple 服务](media/create-cloudsimple-service-search.png)

3. 选择**CloudSimple 服务**。

4. 单击**添加**若要创建新的服务。

    ![添加 CloudSimple 服务](media/create-cloudsimple-service-add.png)

5. 选择你想要创建 CloudSimple 服务的订阅。

6. 选择该服务的资源组。 若要添加新的资源组，请单击**创建新**。

7. 输入名称以标识该服务。

8. 输入服务网关 CIDR。 指定/28 子网不重叠的任何现有子网。  其中包括在本地子网，Azure 子网，或任何计划 CloudSimple 子网。 创建服务后，您无法更改 CIDR。

    ![创建 CloudSimple 服务](media/create-cloudsimple-service.png)

9. 单击“确定”。 

创建服务并将其添加到服务的列表。

## <a name="next-steps"></a>后续步骤

* 了解如何[创建私有云](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)
