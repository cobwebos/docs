---
title: Azure VMware 解决方案 (按 CloudSimple)-访问 vSphere 客户端
description: 描述如何访问私有云的 vCenter。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196122"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>访问私有云 vCenter 门户

可以从 Azure 门户或 CloudSimple 门户启动私有云 vCenter 门户。  通过 vCenter 门户, 你可以管理私有云上的 VMware 基础结构。

## <a name="before-you-begin"></a>开始之前

必须建立网络连接, 并且必须启用 DNS 名称解析才能访问 vCenter 门户。  可以使用以下任一选项建立与私有云的网络连接。

* [使用 ExpressRoute 从本地连接到 CloudSimple](on-premises-connection.md)
* [配置到 CloudSimple 私有云的 VPN 连接](set-up-vpn.md)

若要设置私有云 VMware 基础结构组件的 DNS 名称解析, 请参阅[为来自本地工作站的私有云 vCenter 访问配置 DNS 进行名称解析](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-vcenter-from-azure-portal"></a>从 Azure 门户访问 vCenter

可以从 Azure 门户启动私有云的 vCenter 门户。

1. 选择“所有服务”。

2. 搜索 " **CloudSimple Services**"。

3. 选择你想要连接到的私有云的 CloudSimple 服务。

4. 在 "**概述**" 页上, 单击 "**查看 VMware 私有云**"

    ![CloudSimple 服务概述](media/cloudsimple-service-overview.png)

5. 从私有云列表中选择 "私有云", 然后单击 "**启动 VSphere 客户端**"。

    ![启动 vSphere 客户端](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>从 CloudSimple 门户访问 vCenter

可以从 CloudSimple 门户启动私有云的 vCenter 门户。

1. 访问[CloudSimple 门户](access-cloudsimple-portal.md)。

2. 从**资源**中选择要访问的私有云, 然后单击 "**启动 vSphere 客户端**"。

    ![启动 vSphere 客户端-资源](media/cloudsimple-portal-resources-launch-vcenter.png)

3. 你还可以从私有云的摘要屏幕启动 vCenter 门户。

    ![启动 vSphere 客户端-摘要](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>后续步骤

* [创建和管理私有云的 Vlan/子网](create-vlan-subnet.md)
* [VMware vCenter 的 CloudSimple 私有云权限模型](learn-private-cloud-permissions.md)