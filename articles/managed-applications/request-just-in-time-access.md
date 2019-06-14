---
title: 启用并为 Azure 托管应用程序请求中实时访问
description: 介绍 Azure 托管应用程序的发布服务器请求中实时访问托管应用程序的方式。
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: ea933f5382cb42c01de523326b094c1813401132
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481769"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>启用并为 Azure 托管应用程序请求中实时访问

托管应用程序的使用者可能会愿意授予对托管的资源组的永久访问权限。 作为发布服务器的管理器应用程序，您可能更愿意使用者知道完全需要访问托管的资源。 为了让使用者更好地授予对托管资源，Azure 托管应用程序的访问控制提供了名为在实时 (JIT) 访问，目前以预览版提供的功能。

JIT 访问，您可以请求进行故障排除或维护提升到托管应用程序的资源的访问权限。 始终对资源的只读访问，但在特定时间段内可以有更高的访问。

授予访问权限的工作流是：

1. 添加到应用商店的托管应用程序，并指定 JIT 访问可用。

1. 在部署期间，使用者启用 JIT 访问托管应用程序实例。

1. 在部署后，使用者可以更改针对 JIT 访问设置。

1. 需要进行故障排除或更新托管的资源时发送访问请求。

1. 使用者批准你的请求。

本文重点介绍发布者需要启用 JIT 访问，并提交请求的操作。 若要了解有关审批 JIT 访问请求信息，请参阅[批准在 Azure 托管应用程序中实时访问](approve-just-in-time-access.md)。

## <a name="add-jit-access-step-to-ui"></a>向 UI 添加 JIT 访问权限的步骤

CreateUiDefinition.json 文件是与所创建的永久访问权限的用户界面文件完全相同，只不过必须包括一个步骤，可让使用者启用 JIT 访问。 若要了解有关发布产品/服务在 Azure Marketplace 中第一个托管应用程序的详细信息，请参阅[Azure Marketplace 中的管理应用程序](publish-marketplace-app.md)。

若要支持产品/服务的 JIT 功能，请将以下内容添加到 CreateUiDefinition.json 文件：

在"步骤":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
在"输出":

```json
"jitAccessPolicy": "[parse(concat('{\"jitAccessEnabled\":', string(steps('jitConfiguration').jitConfigurationControl.jitEnabled), ',\"jitApprovalMode\":\"', steps('jitConfiguration').jitConfigurationControl.jitApprovalMode, '\",\"maximumJitAccessDuration\":\"', steps('jitConfiguration').jitConfigurationControl.maxAccessDuration, '\",\"jitApprovers\":', string(steps('jitConfiguration').jitConfigurationControl.approvers), '}'))]"
```

> [!NOTE]
> JIT 访问处于预览状态。 JIT 配置的架构可以更改在将来的迭代。

## <a name="enable-jit-access"></a>启用 JIT 访问

在定义产品/服务在 marketplace 中时，请确保启用 JIT 访问。

1. 登录到[云合作伙伴发布门户](https://cloudpartner.azure.com)。

1. 提供在 marketplace 中发布托管应用程序的值。 选择**是**为**启用 JIT 访问？**

   ![启用实时访问](./media/request-just-in-time-access/marketplace-enable.png)

已添加到您的 UI，JIT 配置步骤，并已启用 JIT 访问 marketplace 产品/服务中。 当使用者部署托管应用程序时，他们可以[启用对其实例的 JIT 访问](approve-just-in-time-access.md#enable-during-deployment)。

## <a name="request-access"></a>请求访问权限

当需要访问使用者的托管的资源时，发送请求的特定角色、 时间和持续时间。 然后，使用者必须批准该请求。

若要发送的 JIT 访问请求：

1. 选择**JIT 访问**访问所需的托管应用程序。

1. 选择**符合条件的角色**，然后选择**激活**在操作列中的所需的角色。

   ![激活访问的请求](./media/request-just-in-time-access/send-request.png)

1. 上**激活角色**窗体中，选择开始时间和持续时间为角色处于活动状态。 选择**激活**将请求发送。

   ![激活访问权限](./media/request-just-in-time-access/activate-access.png) 

1. 查看通知，以了解新的 JIT 请求已成功发送到使用者。

   ![通知](./media/request-just-in-time-access/in-progress.png)

   现在，你必须等待到使用者[批准请求](approve-just-in-time-access.md#approve-requests)。

1. 若要查看托管应用程序的所有 JIT 请求状态，请选择**JIT 访问**并**请求历史记录**。

   ![查看状态](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>已知问题

请求 JIT 访问权限的帐户的主体 ID 必须显式包含在托管应用程序定义。 帐户不能只是包含通过包中指定的组。 将在未来的版本中解决此限制。

## <a name="next-steps"></a>后续步骤

若要了解有关审批请求针对 JIT 访问，请参阅[批准在 Azure 托管应用程序中实时访问](approve-just-in-time-access.md)。
