---
title: Microsoft Azure Data Box Disk 自我托管交付 | Microsoft Docs
description: 描述 Azure Data Box Disk 设备的自我托管交付工作流
services: databox
author: priestlg
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: v-grpr
ms.openlocfilehash: 95c0a884b335920df9803a9b468ab522646b26a1
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258761"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>在 Azure 门户中对 Azure Data Box Disk 使用自我托管交付功能

本文介绍了 Azure Data Box Disk 中下单、提货和交货的自我托管交付任务。 你可使用 Azure 门户来管理 Data Box Disk。

## <a name="prerequisites"></a>先决条件

[订购 Azure Data Box Disk](data-box-disk-deploy-ordered.md) 时，可选择“自我托管交付”选项。 “自我托管交付”功能仅在以下区域中提供：

* 美国政府
* 欧洲西部
* 日本
* 新加坡
* 韩国
* 南非
* 印度 (预览) 

## <a name="use-self-managed-shipping"></a>使用自我托管交付

在下单购买 Data Box Disk 时，可选择“自我托管交付”选项。

1. 在 Azure Data Box Disk 订单的“联系人详细信息”下，选择“+添加送货地址” 。

   ![自我托管交付](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 选择送货类型时，请选择“自我托管交付”选项。 仅当你位于先决条件中所述的受支持的区域时，此选项才可用。

3. 提供送货地址后，需要验证该地址并完成下单。

   ![自我托管交付](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. 准备好设备并收到电子邮件通知后，可以计划装货。 在 Azure Data Box Disk 订单中，转到“概述”，然后选择“安排提货” 。

   ![将 Data Box 设备排序以便提货](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. 按照“Azure 提货安排”中的说明进行操作。 你必须发送电子邮件至 [adbops@microsoft.com](mailto:adbops@microsoft.com)，安排从你所在区域的数据中心来提取设备，然后才可获得授权代码。

   ![安排提货](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. 安排好设备提货后，可在“Azure 提货安排”中查看授权代码。

   ![查看授权代码](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   记下此授权代码。

   根据安全要求，在制定计划时，需要提供将获得 pick 的人员的姓名，这是必需的。

   你还需要提供将前往数据中心提货的人员的详细信息。 你或联系人员必须携带政府批准的带照片的 ID，我们将在数据中心验证该 ID。

   此外，设备提取人员还需要有授权代码。 授权代码对于分拣或拖放是唯一的，在数据中心进行验证。

7. 从数据中心提取设备后，你的订单将自动切换到“已提货”状态。

   ![已提货](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. 提取设备后，可将数据复制到你所在站点上的 Data Box Disk。 数据复制完成后，可准备寄送 Data Box Disk。

   完成数据复制后，你需要与运营部门联系，安排交货预约。 你需要分享要前往数据中心归还磁盘的人员的详细信息。 此外，数据中心需要在交货时验证授权代码。 Azure 门户的“安排交货”下将提供交货的授权代码。

   > [!NOTE]
   > 请勿通过电子邮件共享授权代码。 仅在数据中心交货时进行验证该代码。

9. 如果你已收到订单交货预约，那么订单在 Azure 门户中现应处于“可在 Azure 数据中心接收”状态。

   ![查看授权代码](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. 在 ID 和授权代码经过验证，且你在数据中心归还设备后，订单的状态应为“已接收”。

    ![接收完成](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. 设备被接收后，数据复制将继续。 复制完成后，订单完成。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户部署 Azure Data Box Disk](data-box-disk-quickstart-portal.md)
