---
title: 将 Azure 订阅映射到 Azure 的 CloudSimple VMware 解决方案上的资源池
description: 描述如何将 Azure CloudSimple VMware 解决方案上的资源池映射到 Azure 订阅
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efda996e03d46a2f97d19558f7c2930b623a639e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333148"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>映射到 Azure 订阅从您的私有云的资源池

Azure 订阅映射可以映射到 Azure 订阅从私有云 vCenter 资源池。 您可以映射仅在其中创建 CloudSimple 服务订阅。  从 Azure 门户创建 VMware 虚拟机部署中映射的资源池的虚拟机。  在 CloudSimple 门户中，可以查看和管理私有云的 Azure 订阅。

订阅可以映射到多个 vCenter 资源池的私有云。  您必须映射每个私有云的资源池。  仅映射的资源池将可用于从 Azure 门户创建 VMware 虚拟机。

> [!IMPORTANT]
> 映射资源池还会将映射的所有子资源池。 如果已映射的所有子资源池，不能都映射父资源池。

## <a name="before-you-begin"></a>开始之前

本文假定你的订阅中有 CloudSimple 服务和私有云。  若要创建 CloudSimple 服务，请参阅[快速入门-创建服务](quickstart-create-cloudsimple-service.md)。  如果需要创建私有云，请参阅[快速入门-配置私有云环境](quickstart-create-private-cloud.md)。

可以将 vCenter 群集 （根资源池） 映射到你的订阅。  如果你想要创建新的资源池，请参阅[创建资源池](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html)VMware 文档网站上的文章。

## <a name="default-resource-group"></a>默认资源组

从 Azure 门户创建新的 CloudSimple 虚拟机时，可选择的资源组。  私有云 vCenter 中映射的资源池上创建的虚拟机将在 Azure 门户上可见。  发现的虚拟机都位于默认 Azure 资源组。  您可以更改默认资源组的名称。

## <a name="map-azure-subscription"></a>映射 Azure 订阅

1. 访问[CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开**资源**页上，选择你想要映射的私有云。

3. 选择**映射的 Azure 订阅**。

4. 单击**编辑 Azure 订阅映射**。

5. 若要映射可用的资源池，在左侧选择它们，然后单击向右箭头。

6. 若要删除映射，请在右侧选择它们，然后单击向左箭头。

    ![Azure 订阅](media/resources-azure-mapping.png)

7. 单击“确定”。 

## <a name="change-default-resource-group-name"></a>更改默认资源组名称

1. 访问[CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开**资源**页上，选择你想要映射的私有云。

3. 选择**映射的 Azure 订阅**。

4. 单击**编辑**下 Azure 资源组名称。

    ![编辑资源组名称](media/resources-edit-resource-group-name.png)

5. 输入资源组的新名称，然后单击**提交**。

    ![输入新资源组名称](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 详细了解[CloudSimple 虚拟机](cloudsimple-virtual-machines.md)