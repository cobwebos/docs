---
title: Microsoft Azure 的客户密码箱
description: Microsoft Azure 的客户密码箱技术概述，在 Microsoft 可能需要访问客户数据时提供对云提供商访问的控制。
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: 5330c751aaa3fcbd5c7fc268e4a4de08d336d474
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735430"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Microsoft Azure 的客户密码箱

> [!NOTE]
> 若要使用此功能，你的组织必须具有最小**开发人员**级别的[Azure 支持计划](https://azure.microsoft.com/support/plans/)。

Microsoft Azure 的客户密码箱提供了一个界面，供客户查看和批准或拒绝客户数据访问请求。 当 Microsoft 工程师需要在支持请求期间访问客户数据时，可以使用此方法。

本文介绍如何启动、跟踪和存储客户密码箱请求，以便以后查看和审核。

客户密码箱现已正式发布，目前已启用对虚拟机的远程桌面访问。

## <a name="workflow"></a>工作流

以下步骤概述了客户密码箱请求的典型工作流。

1. 组织中的某人对其 Azure 工作负荷有问题。

2. 在此人员对问题进行故障排除但无法修复后，他们将从[Azure 门户](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)中打开支持票证。 票证分配给 Azure 客户支持工程师。

3. Azure 支持工程师会查看服务请求，并确定解决此问题的后续步骤。

4. 如果支持工程师无法通过使用标准工具和遥测对问题进行故障排除，则下一步是通过使用实时（JIT）访问服务请求提升的权限。 此请求可以来自原始支持工程师。 或者，它可以来自不同的工程师，因为此问题已上报给 Azure DevOps 团队。

5. Azure 工程师提交访问请求后，实时服务会评估请求，其中包括以下因素：
    - 资源的范围
    - 请求者是隔离标识还是使用多重身份验证
    - 权限级别

    此请求还可以基于 JIT 规则，包括来自内部 Microsoft 审批者的批准。 例如，审批者可以是客户支持主管或 DevOps 经理。

6. 当请求需要直接访问客户数据时，将启动客户密码箱请求。 例如，对客户的虚拟机的远程桌面访问。

    请求现在处于**客户通知**状态，在授予访问权限前等待客户的批准。

7. 在客户组织中，拥有 Azure 订阅的[所有者角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)的用户将收到来自 Microsoft 的电子邮件，通知他们有关挂起的访问请求。 对于客户密码箱请求，此人为指定的审批者。

    示例电子邮件：

    ![Azure 客户密码箱-电子邮件通知](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. 电子邮件通知提供 Azure 门户中**客户密码箱**边栏选项卡的链接。 使用此链接，指定的审批者可以登录到 Azure 门户，查看其组织为客户密码箱所做的任何挂起的请求：

    ![Azure 客户密码箱-登陆页面](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   请求在客户队列中保留四天。 此时间过后，访问请求会自动过期，并且不会向 Microsoft 工程师授予任何访问权限。

9. 若要获取待定请求的详细信息，指定的审批者可以从**挂起的请求**中选择密码箱请求：

    ![Azure 客户密码箱-查看挂起的请求](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. 指定的审批者还可以选择**服务请求 ID** ，以查看原始用户创建的支持票证请求。 此信息提供了有关 Microsoft 支持部门的原因以及所报告问题的历史记录的上下文。 例如：

    ![Azure 客户密码箱-查看支持票证请求](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. 查看请求后，指定的审批者选择 "**批准**" 或 "**拒绝**"：

    ![Azure 客户密码箱-选择 "批准" 或 "拒绝"](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    作为所选内容的结果：
    - **批准**：向 Microsoft 工程师授予访问权限。 在八小时的默认期限内授予访问权限。
    - **拒绝**： Microsoft 工程师的提升访问请求被拒绝，无需执行其他操作。

出于审核目的，在此工作流中执行的操作记录在[客户密码箱请求日志](#auditing-logs)中。

## <a name="auditing-logs"></a>审核日志

客户密码箱日志存储在活动日志中。 在 Azure 门户中，选择 "**活动日志**" 以查看与客户密码箱请求相关的审核信息。 可以筛选特定操作，例如：
- **拒绝密码箱请求**
- **创建密码箱请求**
- **审批密码箱请求**
- **密码箱请求过期**

示例：

![Azure 客户密码箱活动日志](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>公开上市中支持的服务和方案

以下服务和方案目前在客户密码箱公开上市。

### <a name="remote-desktop-access-to-virtual-machines"></a>对虚拟机的远程桌面访问

当前已对虚拟机的远程桌面访问请求启用客户密码箱。 支持以下工作负载：
- 平台即服务（PaaS）-Azure 云服务（web 角色和辅助角色）
- 基础结构即服务（IaaS）-Windows 和 Linux （仅限 Azure 资源管理器）
- 虚拟机规模集-Windows 和 Linux

> [!NOTE]
> 客户密码箱不支持 IaaS 经典实例。 如果你的工作负荷在 IaaS 经典实例上运行，我们建议你将其从经典部署模型迁移到资源管理器部署模型。 有关说明，请参阅[平台支持的从经典部署模型到 Azure 资源管理器的 IaaS 资源迁移](../../virtual-machines/windows/migration-classic-resource-manager-overview.md)。

#### <a name="detailed-audit-logs"></a>详细审核日志

对于涉及远程桌面访问的方案，你可以使用 Windows 事件日志来查看 Microsoft 工程师执行的操作。 请考虑使用 Azure 安全中心收集事件日志，并将数据复制到工作区进行分析。 有关详细信息，请参阅[Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md)。

## <a name="supported-services-and-scenarios-in-preview"></a>预览版中支持的服务和方案

以下服务目前以预览版提供客户密码箱：

- Azure 存储

- Azure SQL DB

- Azure 数据资源管理器

- 虚拟机（现在还包括对内存转储和托管磁盘的访问权限）

- Azure 订阅传输

若要为组织的这些预览版提供客户密码箱，请注册[Azure 公共预览版客户密码箱](https://aka.ms/customerlockbox/insiderprogram)。


## <a name="exclusions"></a>排除项

不会在以下工程支持方案中触发客户密码箱请求：

- Microsoft 工程师需要执行超出标准操作过程的活动。 例如，在意外或不可预测方案中恢复或还原服务。

- Microsoft 工程师将在故障排除过程中访问 Azure 平台，并且无意中有权访问客户数据。 例如，Azure 网络团队会执行故障排除，导致网络设备上的数据包捕获。 但是，如果客户在传输数据时对数据进行了加密，则工程师无法读取数据。

## <a name="next-steps"></a>后续步骤

对于具有最小**开发人员**的[Azure 支持计划](https://azure.microsoft.com/support/plans/)的所有客户，客户密码箱自动提供。

如果你具有符合条件的支持计划，则无需执行任何操作即可启用客户密码箱。 如果需要执行此操作来处理从组织中的某个人中存档的支持票证，则客户密码箱请求将由 Microsoft 工程师发起。
