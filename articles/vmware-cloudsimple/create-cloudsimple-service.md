---
title: Azure VMware 解决方案（按 CloudSimple）-创建 CloudSimple 服务
description: 描述如何在 Azure 门户中创建 CloudSimple 服务
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 99194c42dbc6ef07301be517021bf0fb4b4e7c23
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173501"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>通过 CloudSimple 服务创建 Azure VMware 解决方案

若要通过 CloudSimple 开始使用 Azure VMware 解决方案，请在 Azure 门户中创建 Azure VMware 解决方案 by CloudSimple 服务。

> [!IMPORTANT]
> 在创建 CloudSimple 服务之前，必须在 Azure 订阅上注册 VMwareCloudSimple 资源提供程序。 按照在[Azure 订阅中启用 VMwareCloudSimple 资源提供程序](enable-cloudsimple-service.md)中的步骤进行操作。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-the-service"></a>创建服务

1. 选择“所有服务”。
2. 搜索 " **CloudSimple Services**"。
    @no__t 0Search CloudSimple Service @ no__t-1
3. 选择**CloudSimple Services**。
4. 单击 "**添加**" 创建新服务。
    @no__t 0Add CloudSimple Service @ no__t-1
5. 选择要在其中创建 CloudSimple 服务的订阅。
6. 选择服务的资源组。 若要添加新的资源组，请单击 "**新建**"。
7. 输入名称以标识该服务。
8. 输入服务网关的 CIDR。 指定不与任何本地子网、Azure 子网或计划 CloudSimple 子网重叠的 a/28 子网。 创建服务后无法更改 CIDR。

    ![创建 CloudSimple 服务](media/create-cloudsimple-service.png)
9. 单击 **“确定”** 。

创建服务并将其添加到服务列表中。

## <a name="next-steps"></a>后续步骤

* 了解如何[预配节点](create-nodes.md)
* 了解如何[创建私有云](create-private-cloud.md)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)
