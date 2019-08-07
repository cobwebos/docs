---
title: 通过 CloudSimple 将 azure 订阅映射到 Azure VMware 解决方案上的资源池
description: 介绍如何通过 CloudSimple 将 Azure VMware 解决方案上的资源池映射到你的 Azure 订阅
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1bf721f35500d2ff1344996e7750c5e574f40f31
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816262"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>将资源池从私有云映射到你的 Azure 订阅

Azure 订阅映射允许将资源池从私有云 vCenter 映射到你的 Azure 订阅。 你可以仅映射已创建 CloudSimple 服务的订阅。  从 Azure 门户创建 VMware 虚拟机将部署映射的资源池中的虚拟机。  在 CloudSimple 门户中, 可以查看和管理私有云的 Azure 订阅。

订阅可以映射到私有云的多个 vCenter 资源池。  必须映射每个私有云的资源池。  只有映射的资源池可用于从 Azure 门户创建 VMware 虚拟机。

> [!IMPORTANT]
> 映射资源池还会映射任何子资源池。 如果已映射任何子资源池, 则无法映射父资源池。

## <a name="before-you-begin"></a>开始之前

本文假设你的订阅中有 CloudSimple 服务和私有云。  若要创建 CloudSimple 服务, 请参阅[快速入门-创建服务](quickstart-create-cloudsimple-service.md)。  如果需要创建私有云, 请参阅[快速入门-配置私有云环境](quickstart-create-private-cloud.md)。

可以将 vCenter 群集 (根资源池) 映射到你的订阅。  如果要创建新的资源池, 请参阅 VMware 文档站点上[的创建资源池一](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html)文。

## <a name="default-resource-group"></a>默认资源组

通过 Azure 门户创建新的 CloudSimple 虚拟机, 你可以选择资源组。  在已映射的资源池中的私有云 vCenter 上创建的虚拟机将在 Azure 门户上可见。  发现的虚拟机将放置在默认 Azure 资源组中。  你可以更改默认资源组的名称。

## <a name="map-azure-subscription"></a>映射 Azure 订阅

1. 访问[CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开 "**资源**" 页, 然后选择要映射的私有云。

3. 选择 " **Azure 订阅映射**"。

4. 单击 "**编辑 Azure 订阅映射**"。

5. 若要映射可用的资源池, 请在左侧选择它们, 然后单击右箭头。

6. 若要删除映射, 请在右侧选择它们, 然后单击左箭头。

    ![Azure 订阅](media/resources-azure-mapping.png)

7. 单击 **“确定”** 。

## <a name="change-default-resource-group-name"></a>更改默认资源组名称

1. 访问[CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开 "**资源**" 页, 然后选择要映射的私有云。

3. 选择 " **Azure 订阅映射**"。

4. 单击 "Azure 资源组名称" 下的 "**编辑**"。

    ![编辑资源组名称](media/resources-edit-resource-group-name.png)

5. 输入资源组的新名称, 然后单击 "**提交**"。

    ![输入新的资源组名称](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>后续步骤

* [在 Azure 上使用 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 了解有关[CloudSimple 虚拟机](cloudsimple-virtual-machines.md)的详细信息