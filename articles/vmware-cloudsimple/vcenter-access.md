---
title: Azure VMware 解决方案（AVS）-Access vSphere client
description: 介绍如何从 AVS 私有云访问 vCenter。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022658"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>访问你的 AVS 私有云 vCenter 门户

可以从 Azure 门户或 AVS 门户启动 AVS 私有云 vCenter 门户。 vCenter 门户可用于管理你的 AVS 私有云上的 VMware 基础结构。

## <a name="before-you-begin"></a>开始之前

必须建立网络连接，并且必须启用 DNS 名称解析才能访问 vCenter 门户。 可以使用以下任一选项建立与 AVS 私有云的网络连接。

* [使用 ExpressRoute 从本地连接到 AVS](on-premises-connection.md)
* [配置与 AVS 私有云的 VPN 连接](set-up-vpn.md)

若要设置你的 AVS 私有云 VMware 基础结构组件的 DNS 名称解析，请参阅[为来自本地工作站的 Avs 私有云 vCenter 访问配置 dns 进行名称解析](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-vcenter-from-azure-portal"></a>从 Azure 门户访问 vCenter

可以从 Azure 门户启动 AVS 私有云的 vCenter 门户。

1. 选择“所有服务”。

2. 搜索**AVS 服务**。

3. 选择要连接到的 AVS 私有云的 AVS 服务。

4. 在 "**概述**" 页上，单击 "**查看 VMware AVS 私有云**"

    ![AVS 服务概述](media/cloudsimple-service-overview.png)

5. 从 AVS 私有云列表中选择 "AVS 私有云"，然后单击 "**启动 VSphere 客户端**"。

    ![启动 vSphere 客户端](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>从 AVS 门户访问 vCenter

可以从 AVS 门户启动 AVS 私有云的 vCenter 门户。

1. 访问你的[AVS 门户](access-cloudsimple-portal.md)。

2. 从**资源**中，选择要访问的 AVS 私有云，然后单击 "**启动 vSphere 客户端**"。

    ![启动 vSphere 客户端-资源](media/cloudsimple-portal-resources-launch-vcenter.png)

3. 你还可以从你的 AVS 私有云的摘要屏幕启动 vCenter 门户。

    ![启动 vSphere 客户端-摘要](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>后续步骤

* [创建和管理你的 AVS 私有云的 Vlan/子网](create-vlan-subnet.md)
* [VMware vCenter 的 AVS 私有云权限模型](learn-private-cloud-permissions.md)