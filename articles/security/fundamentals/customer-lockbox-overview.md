---
title: 适用于 Microsoft Azure 的客户密码箱
description: Microsoft Azure 的客户密码箱的技术概述，该平台提供 Microsoft 可能需要访问客户数据时对云提供商访问的控制。
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: abc16ae7f7ab8bf15173248a6e7668e689e127de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561963"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>适用于 Microsoft Azure 的客户密码箱

> [!NOTE]
> 要使用此功能，您的组织必须具有具有最小级别的**开发人员**的[Azure 支持计划](https://azure.microsoft.com/support/plans/)。

Microsoft Azure 的客户密码箱提供了一个界面，供客户查看、批准或拒绝客户数据访问请求。 它用于 Microsoft 工程师需要在支持请求期间访问客户数据的情况。

本文介绍如何启动、跟踪和存储客户密码箱请求，以便以后进行审核和审核。

客户密码箱现在通常可用，并且当前已启用，以便远程桌面访问虚拟机。

## <a name="workflow"></a>工作流

以下步骤概述了客户密码箱请求的典型工作流。

1. 组织中的某个人在其 Azure 工作负荷方面出现问题。

2. 此人排除故障但无法修复后，他们会从[Azure 门户](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)打开支持票证。 票证分配给 Azure 客户支持工程师。

3. Azure 支持工程师会检查服务请求并确定解决问题的后续步骤。

4. 如果支持工程师无法使用标准工具和遥测来解决问题，则下一步是使用实时 （JIT） 访问服务请求提升的权限。 此请求可以来自原始支持工程师。 或者，它可以来自不同的工程师，因为问题已上报到 Azure DevOps 团队。

5. Azure 工程师提交访问请求后，实时服务会评估请求，同时考虑到以下因素：
    - 资源的范围
    - 请求者是隔离标识还是使用多重身份验证
    - 权限级别

    根据 JIT 规则，此请求还可能包括来自内部 Microsoft 审批者的批准。 例如，审批人可能是客户支持主管或 DevOps 经理。

6. 当请求需要直接访问客户数据时，将启动客户密码箱请求。 例如，远程桌面访问客户的虚拟机。

    请求现在处于 **"客户通知"** 状态，等待客户批准，然后再授予访问权限。

7. 在客户组织中，具有 Azure 订阅[的所有者角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles)的用户会收到来自 Microsoft 的电子邮件，以通知他们挂起的访问请求。 对于客户密码箱请求，此人是指定的审批人。

    示例电子邮件：

    ![Azure 客户密码箱 - 电子邮件通知](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. 电子邮件通知提供指向 Azure 门户中的 **"客户锁定框**"边栏选项卡的链接。 使用此链接，指定的审批者登录到 Azure 门户以查看其组织为客户锁定框提供的任何挂起请求：

    ![Azure 客户密码箱 - 着陆页](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   请求在客户队列中保留四天。 在此时间之后，访问请求将自动过期，并且不会授予 Microsoft 工程师访问权限。

9. 要获取挂起请求的详细信息，指定审批人可以从 **"挂起的请求**"中选择密码箱请求：

    ![Azure 客户密码箱 - 查看挂起的请求](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. 指定的审批人还可以选择**服务请求 ID**来查看由原始用户创建的支持票证请求。 此信息提供了 Microsoft 支持参与原因以及报告问题的历史记录的上下文。 例如：

    ![Azure 客户密码箱 - 查看支持票证请求](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. 审核请求后，指定审批人选择**批准**或**拒绝**：

    ![Azure 客户密码箱 - 选择"批准"或"拒绝"](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    作为选择的结果：
    - **批准**：授予 Microsoft 工程师访问权限。 授予访问权限的默认期限为 8 小时。
    - **拒绝**： Microsoft 工程师的提升访问请求被拒绝，并且不采取进一步操作。

出于审核目的，在此工作流中执行的操作将记录在[客户密码箱请求日志](#auditing-logs)中。

## <a name="auditing-logs"></a>审核日志

客户密码箱日志存储在活动日志中。 在 Azure 门户中，选择**活动日志**以查看与客户密码箱请求相关的审核信息。 您可以筛选特定操作，例如：
- **拒绝密码箱请求**
- **创建密码箱请求**
- **批准密码箱请求**
- **锁盒请求过期**

示例：

![Azure 客户密码箱 - 活动日志](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>支持的服务和方案在一般可用性

以下服务和方案当前处于客户密码箱的通用可用性。

### <a name="remote-desktop-access-to-virtual-machines"></a>对虚拟机的远程桌面访问

客户密码箱当前已启用，用于对虚拟机的远程桌面访问请求。 支持以下工作负载：
- 平台即服务 （PaaS） - Azure 云服务（Web 角色和辅助角色）
- 基础架构即服务 （IaaS） - Windows 和 Linux（仅限 Azure 资源管理器）
- 虚拟机规模集 - Windows 和 Linux

> [!NOTE]
> 客户密码箱不支持 IaaS 经典实例。 如果在 IaaS 经典实例上运行工作负载，我们建议您将它们从经典版式迁移到资源管理器部署模型。 有关说明，请参阅[平台支持的 IaaS 资源从经典迁移到 Azure 资源管理器](../../virtual-machines/windows/migration-classic-resource-manager-overview.md)。

#### <a name="detailed-audit-logs"></a>详细的审核日志

对于涉及远程桌面访问的方案，可以使用 Windows 事件日志查看 Microsoft 工程师执行的操作。 请考虑使用 Azure 安全中心收集事件日志并将数据复制到工作区进行分析。 有关详细信息，请参阅[Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md)。

## <a name="supported-services-and-scenarios-in-preview"></a>预览版中支持的服务和方案

以下服务当前处于"客户密码箱"的预览版中：

- Azure 存储

- Azure SQL DB

- Azure 数据资源管理器

- 虚拟机（现在还涵盖对内存转储和托管磁盘的访问）

- Azure 订阅传输

要为组织的这些预览产品启用客户密码箱，请注册[Azure 公共预览的客户密码箱](https://aka.ms/customerlockbox/insiderprogram)。


## <a name="exclusions"></a>排除项

以下工程支持方案中不会触发客户密码箱请求：

- Microsoft 工程师需要执行超出标准操作程序的活动。 例如，在意外或不可预测的情况下恢复或还原服务。

- Microsoft 工程师访问 Azure 平台作为故障排除的一部分，无意中可以访问客户数据。 例如，Azure 网络团队执行故障排除，导致网络设备上的数据包捕获。 但是，如果客户在传输过程中对数据进行加密，工程师将无法读取数据。

## <a name="next-steps"></a>后续步骤

客户密码箱自动适用于所有具有[Azure 支持计划](https://azure.microsoft.com/support/plans/)且具有最小**开发人员级别的客户**。

当您有符合条件的支持计划时，您无需执行任何操作即可启用客户密码箱。 如果需要此操作来进度从组织中的某个人提交的支持票证，则客户锁定框请求由 Microsoft 工程师启动。
