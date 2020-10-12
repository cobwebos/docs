---
title: 部署 Azure VMware 解决方案
description: 使用 Azure 门户部署 Azure VMware 解决方案的步骤。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 4d0881721cd8c13d1b6c9fb3a29e4cdb6d6a753f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578303"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“创建新资源”。 在“搜索市场”文本框中键入 `Azure VMware Solution`，然后从列表中选择“Azure VMware 解决方案” 。 在“Azure VMware 解决方案”窗口中选择“创建” 。

1. 在“基本信息”选项卡上，输入相关字段的值。 下表列出了字段的属性。

   | 字段   | 值  |
   | ---| --- |
   | **订阅** | 你打算用于部署的订阅。|
   | **资源组** | 私有云资源的资源组。 |
   | **位置** | 选择一个位置，例如“美国东部”。|
   | **资源名称** | Azure VMware 解决方案私有云的名称。 |
   | **SKU** | 选择以下 SKU 值：AV36 |
   | **主机** | 要添加到私有云群集的主机数。 默认值为 3，在部署后可以增大或减小此值。  |
   | **vCenter 管理员密码** | 输入云管理员密码。 |
   | **NSX-T 管理者密码** | 输入 NSX-T 管理员密码。 |
   | **地址块** | 输入私有云 CIDR 网络的 IP 地址块,，例如 10.175.0.0/22。 |
   | **虚拟网络** | 选择一个虚拟网络，或者为 Azure VMware 解决方案私有云创建一个新的虚拟网络。  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="在“基本信息”选项卡上，输入相关字段的值。" border="true":::

1. 完成后，选择“审阅 + 创建”。 在下一个屏幕上，验证输入的信息。 如果信息全部正确，请选择“创建”。

   > [!NOTE]
   > 此步骤大约需要两小时。 

1. 验证部署是否成功。 导航到创建的资源组，然后选择私有云。  完成部署后，你将看到“成功”状态。 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="在“基本信息”选项卡上，输入相关字段的值。" border="true":::