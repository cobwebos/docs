---
title: "在 Azure 中使用 Windows 客户端映像 | Microsoft Docs"
description: "如何使用 Visual Studio 订阅权益，在 Azure 中针对开发/测试方案部署 Windows 7、Windows 8 或 Windows 10"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 207a6562965b4913416bd4dbf3eb132b42938dc9
ms.contentlocale: zh-cn
ms.lasthandoff: 07/10/2017

---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>在 Azure 中使用 Windows 客户端实现开发/测试方案
如果有相应的 Visual Studio（以前为 MSDN）订阅，可以在 Azure 中使用 Windows 7、Windows 8 或 Windows 10 开展开发/测试方案。 本文概述在 Azure 中运行 Windows 客户端和使用 Azure 库映像所要满足的条件。

## <a name="subscription-eligibility"></a>订阅条件
有效的 Visual Studio 订户（已获取 Visual Studio 订阅许可证的用户）可以使用 Windows 客户端进行开发和测试。 可以在自己的硬件上，以及在任何类型的 Azure 订阅中运行的 Azure 虚拟机上使用 Windows 客户端。 不能在 Azure 上针对一般生产目的部署或使用 Windows 客户端，不是有效 Visual Studio 订户的用户也不能使用 Windows 客户端。

为方便起见，我们在 Azure 库的[符合条件的开发/测试产品](#eligible-offers)中提供了某些 Windows 10 映像。 属于任一产品类型的 Visual Studio 订户也可以[适当地准备和创建](prepare-for-upload-vhd-image.md) 64 位 Windows 7、Windows 8 或 Windows 10 映像，然后[上载到 Azure](upload-generalized-managed.md)。 仅限有效的 Visual Studio 订户用于开发/测试目的。

## <a name="eligible-offers"></a>符合条件的产品
下表详细描述了可通过 Azure 库部署 Windows 10 的产品 ID。 只有在以下产品中才能看到 Windows 10 映像。 需要以其他产品类型运行 Windows 客户端的 Visual Studio 订户需要[适当地准备和创建](prepare-for-upload-vhd-image.md) 64 位 Windows 7、Windows 8 或 Windows 10 映像，[然后上载到 Azure](upload-generalized-managed.md)。

| 产品名称 | 产品编号 | 可用客户端映像 |
|:--- |:---:|:---:|
| [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Visual Studio Enterprise (MPN) 订户](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Visual Studio Professional 订户](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Visual Studio Test Professional 订户](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium with MSDN（权益）](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Visual Studio Enterprise 订户](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Visual Studio Enterprise (BizSpark) 订户](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise 开发/测试](https://azure.microsoft.com/ofers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>检查 Azure 订阅
如果不知道自己的产品 ID，可以使用以下两种方式之一通过 Azure 门户获取：  

- 在“订阅”边栏选项卡上：

  ![Azure 门户中的产品 ID 详细信息](./media/client-images/offer-id-azure-portal.png) 

- 或者，单击“计费”，然后单击订阅 ID。 产品 ID 将显示在“计费”边栏选项卡中。

也可以从 Azure 帐户门户的 [订阅](http://account.windowsazure.com/Subscriptions) 选项卡查看产品 ID：

![Azure 帐户门户中的产品 ID 详细信息](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>后续步骤
现在，可以使用 [PowerShell](quick-create-powershell.md)、[Resource Manager 模板](ps-template.md)或 [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) 部署 VM。


