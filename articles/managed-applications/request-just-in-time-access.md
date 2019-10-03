---
title: 启用并请求 Azure 托管应用程序的实时访问
description: 介绍 Azure 托管应用程序的发布者如何请求对托管应用程序的实时访问。
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 0d595d4c96e9f87f1c8eece5d47bf4c8cdd58d7c
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574988"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>启用并请求 Azure 托管应用程序的实时访问

托管应用程序的使用者可能不愿意向你授予对托管资源组的永久访问权限。 作为管理器应用程序的发布者, 你可能更倾向于使用者准确了解需要访问托管资源的时间。 为了使使用者更好地控制授予对托管资源的访问权限, Azure 托管应用程序提供了一种称为实时 (JIT) 访问的功能, 该功能目前处于预览阶段。

JIT 访问允许你请求对托管应用程序的资源的提升访问权限, 以便进行故障排除或维护。 你始终对资源具有只读访问权限, 但对于特定时间段, 你可以获得更高的访问权限。

用于授予访问权限的工作流为:

1. 将托管应用程序添加到 marketplace, 并指定 JIT 访问可用。

1. 在部署过程中, 使用者为托管应用程序的该实例启用 JIT 访问。

1. 部署之后, 使用者可以更改 JIT access 的设置。

1. 需要对托管资源进行故障排除或更新时, 可以发送访问请求。

1. 使用者批准你的请求。

本文重点介绍了发布者执行的操作, 以便启用 JIT 访问和提交请求。 若要了解有关批准 JIT 访问请求的信息, 请参阅[审批 Azure 托管应用程序中](approve-just-in-time-access.md)的实时访问。

## <a name="add-jit-access-step-to-ui"></a>向 UI 添加 JIT 访问步骤

你的 CreateUiDefinition 文件与你为永久访问创建的 UI 文件完全一样, 但你必须包括允许使用者启用 JIT 访问的步骤。 若要详细了解如何在 Azure Marketplace 中发布第一个托管应用程序产品/服务, 请参阅[Marketplace 中的 Azure 托管应用程序](publish-marketplace-app.md)。

若要支持你的产品/服务的 JIT 功能, 请将以下内容添加到 CreateUiDefinition 文件:

在 "步骤" 中:

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
 
在 "输出" 中:

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JIT access 处于预览阶段。 JIT 配置的架构可能会在将来的迭代中发生更改。

## <a name="enable-jit-access"></a>启用 JIT 访问

在 marketplace 中定义产品/服务时, 请确保启用 JIT 访问。

1. 登录到[云合作伙伴发布门户](https://cloudpartner.azure.com)。

1. 提供用于在 marketplace 中发布托管应用程序的值。 选择 **"是"** **启用 JIT 访问？**

   ![启用实时访问](./media/request-just-in-time-access/marketplace-enable.png)

已将 JIT 配置步骤添加到 UI, 并已在 marketplace 产品/服务中启用了 JIT 访问。 当使用者部署托管应用程序时, 他们可以[为其实例启用 JIT 访问](approve-just-in-time-access.md#enable-during-deployment)。

## <a name="request-access"></a>请求访问

需要访问使用者的托管资源时, 需要为特定的角色、时间和持续时间发送请求。 然后, 使用者必须批准该请求。

发送 JIT 访问请求:

1. 为需要访问的托管应用程序选择 " **JIT 访问**"。

1. 选择**合格角色**, 然后在 "操作" 列中选择所需角色的 "**激活**"。

   ![激活访问请求](./media/request-just-in-time-access/send-request.png)

1. 在 "**激活角色**" 窗体中, 选择要激活的角色的开始时间和持续时间。 选择 "**激活**" 以发送请求。

   ![激活访问权限](./media/request-just-in-time-access/activate-access.png) 

1. 查看通知, 查看是否已成功将新的 JIT 请求发送到使用者。

   ![通知](./media/request-just-in-time-access/in-progress.png)

   现在, 你必须等待使用者[批准你的请求](approve-just-in-time-access.md#approve-requests)。

1. 若要查看托管应用程序的所有 JIT 请求的状态, 请选择 " **Jit 访问**" 和 "**请求历史记录**"。

   ![查看状态](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>已知问题

必须在托管应用程序定义中显式包含请求 JIT 访问权限的帐户的主体 ID。 不能通过包中指定的组来包含该帐户。 此限制将在将来的版本中得到解决。

## <a name="next-steps"></a>后续步骤

若要了解有关批准 JIT 访问请求的信息, 请参阅[审批 Azure 托管应用程序中](approve-just-in-time-access.md)的实时访问。
