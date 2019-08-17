---
title: 用 Azure 订阅映射创建资源池
description: 介绍如何通过 Azure 订阅映射为私有云创建资源池
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea102ad8377da70b88a0e59834ebe3a09866632
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563219"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>通过 Azure 订阅映射为私有云创建资源池
Azure 订阅映射允许从可用的 vSphere 资源池为私有云创建资源池。 在 CloudSimple 门户中, 可以查看和管理私有云的 Azure 订阅。

> [!NOTE]
> 映射资源池还会映射任何子资源池。 如果已映射任何子资源池, 则无法映射父资源池。

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)。
2. 打开 "**资源**" 页, 然后选择 " **Azure 订阅映射**"。  
3. 单击 "**编辑 Azure 订阅映射**"。  
4. 若要映射可用的资源池, 请在左侧选择它们, 然后单击右箭头。 
5. 若要删除映射, 请在右侧选择它们, 然后单击左箭头。 

    ![Azure 订阅](media/resources-azure-mapping.png)

6. 单击 **“确定”** 。
