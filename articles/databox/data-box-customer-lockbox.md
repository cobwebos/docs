---
title: 为 Azure Data Box 配置密码箱
description: 了解如何在 Azure Data Box 中使用客户密码箱。
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 9c95760d03db976b59537adcecbe39a942b72126
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86209275"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>使用 Azure Data Box (预览的客户密码箱) 

Azure Data Box 用于将客户数据传输到 Azure 和从 Azure 传输客户数据。 有些情况下，Microsoft 支持部门可能需要在支持请求时访问客户数据。 您可以使用客户密码箱作为界面来查看和批准或拒绝这些数据访问请求。 

本文介绍如何为 Data Box 导入和导出订单启动和跟踪客户密码箱请求。 本文适用于 Azure Data Box 设备和 Azure Data Box Heavy 设备。 

## <a name="devops-workflow-for-data-access"></a>用于数据访问的 DevOps 工作流

Microsoft 的支持和 Data Box 运营团队通常不会访问客户数据。 它们尝试使用标准工具和遥测来解决问题。 <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

如果无法解决问题，并且需要 Microsoft 支持部门调查或修复数据，则这些问题将通过实时 (JIT) 门户请求提升的访问权限。 JIP 门户验证权限级别，提供多重身份验证，同时还包括来自内部 Microsoft 审批者的批准。 例如，审批者可以是 DevOps Manager。 

通过 JIT 门户批准你的提升访问请求后，如果你启用了密码箱，Microsoft 还需要你的明确同意才能访问数据。 通过门户中的客户密码箱服务请求和跟踪访问权限。 

如果尚未启用密码箱，则不需要你同意即可访问数据。


## <a name="prerequisites-for-access-request"></a>访问请求的先决条件

在开始之前，请确保：

1. 按照中的说明创建了 Azure Data Box 顺序：
    1. [教程：按顺序 Azure Data Box](data-box-deploy-ordered.md) 导入订单。
    1. [教程：订单 Azure Data Box](data-box-deploy-export-ordered.md) 的出口订单。

2. 已为 Data Box 配置客户密码箱。 这是一项可选服务。 

    1. 客户密码箱当前以预览版提供 Data Box 服务。 若要为你的组织启用 Data Box 客户密码箱，请注册 [Azure 公共预览版的客户密码箱](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu)。
    2. 对于具有最小开发人员的 Azure 支持计划的所有客户，客户密码箱自动提供。 <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. 已为此问题打开服务请求或支持票证。 有关支持票证的信息，请参阅 [File a service request for Data Box](data-box-disk-contact-microsoft-support.md)。


## <a name="track-approve-request-via-lockbox"></a>跟踪、通过密码箱批准请求

若要跟踪和批准请求以访问客户数据，请执行以下步骤：

1. Microsoft 检测到在 Azure 数据中心上传或下载数据时出现问题。 例如，在 **数据复制** 阶段，Data Box 订单暂停。 

    支持工程师通过支持会话连接到 Data Box，并尝试通过使用标准工具和遥测来解决该问题。 如果 Data Box 磁盘已锁定且共享不可访问，则支持工程师会创建一个密码箱请求。 
 
2. 创建请求时，通常会向订阅管理员发送通知，但你也可以配置通知组。 

3. 你可以在 Azure 门户中看到密码箱请求以获得批准。 

    ![Azure 门户中的请求](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    若要从门户审批密码箱请求，可以选择 " **批准**"。

    ![批准请求](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    批准请求后，会解锁设备磁盘，并可在支持会话中访问共享。

4. 支持工程师解决上传问题，然后禁用支持会话。

解决问题后，数据复制作业将完成。


## <a name="next-steps"></a>后续步骤

- [Microsoft Azure 客户密码箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->

