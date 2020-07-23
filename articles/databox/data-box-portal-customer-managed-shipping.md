---
title: Microsoft Azure Data Box 自我托管交付 | 关于数据的 Microsoft Docs
description: 描述 Azure Data Box 设备的自我托管交付工作流
services: databox
author: priestlg
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: v-grpr
ms.openlocfilehash: 99c7021744467484656c52cf72378c6f553c3913
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84608581"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>在 Azure 门户中对 Azure Data Box 使用自我托管交付功能

本文介绍了 Azure Data Box 设备中下单、提货和交货的自我托管交付任务。 可以使用 Azure 门户管理 Data Box 设备。

## <a name="prerequisites"></a>先决条件

订购 [Azure Data Box](data-box-deploy-ordered.md) 时，可选择“自我托管交付”选项。 “自我托管交付”功能仅在以下区域提供：

* 美国政府
* 欧洲西部
* 日本
* 新加坡
* 韩国

## <a name="use-self-managed-shipping"></a>使用自我托管交付

在下单购买 Data Box 时，可选择“自我托管交付”选项。

1. 在 Azure Data Box 订单的“联系人详细信息”下，选择“+ 添加送货地址” 。
   ![自我托管交付](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 选择送货类型时，请选择“自我托管交付”选项。 仅当你位于受支持的区域（如先决条件中所述）时，此选项才可用。

3. 提供送货地址后，需要验证该地址并完成下单。
   ![自我托管交付](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. 准备好设备并收到电子邮件通知后，可以计划装货。

   在 Azure Data Box 订单中，转到“概述”，然后选择“安排提货” 。

   ![订购 Data Box 设备以便提货](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. 按照“Azure 提货安排”中的说明进行操作。

   你必须发送电子邮件至 [adbops@microsoft.com](mailto:adbops@microsoft.com)，安排从你所在区域的数据中心来提货，然后才可获得授权代码。

   ![安排提货](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. 安排好设备提货后，可在“Azure 提货安排”窗格中查看设备授权代码。

   ![查看授权代码](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   记下此授权代码。 根据安全要求，在制定计划时，有必要显示将到达的人员的姓名，这是必需的。

   你还需要提供将前往数据中心提货的人员的详细信息。 你或联系人员必须携带政府批准的带照片的 ID，我们将在数据中心验证该 ID。

   此外，设备提货人员还需要有授权代码。 在数据中心提货时会验证该授权代码。

7. 从数据中心提出设备后，你的订单将自动切换到“已提货”状态。

    ![已提货](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. 提出设备后，可将数据复制到你所在站点上的 Data Box。 数据复制完成后，可准备交付 Data Box。 有关详细信息，请参阅[准备交付](data-box-deploy-picked-up.md#prepare-to-ship)。

   “准备交付”步骤需要在不出现任何严重错误的情况下完成，否则，需要在做出必要的修复后再次运行此步骤。 准备交付成功完成后，可以在设备本地用户界面上查看交货的授权代码。

   > [!NOTE]
   > 请勿通过电子邮件共享授权代码。 仅在数据中心交货时验证该代码。

9. 如果你已收到订单交货预约，那么订单在 Azure 门户中应处于“可以在 Azure 数据中心接收”状态。 按照“安排交货”下的说明退回设备。

   ![查看授权代码](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. 在你的 ID 和授权代码经过验证，且你将设备交付给数据中心后，订单状态应为“已接收”。

    ![接收完成](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. 设备被接收后，数据复制将继续。 复制完成后，订单完成。

## <a name="next-steps"></a>后续步骤

* [Azure Data Box 入门](data-box-quickstart-portal.md)
