---
title: 请求实时访问
description: 介绍了 Azure 托管应用程序的发布者如何请求对托管应用程序的实时访问权限。
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651379"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>启用和请求对 Azure 托管应用程序的实时访问权限

你的托管应用程序的使用者可能不愿意向你授予对托管资源组的永久访问权限。 作为托管应用程序的发布者，你可能更希望使用者准确了解你何时需要访问托管资源。 为了让使用者在授予对托管资源的访问权限时能够更好地进行控制，Azure 托管应用程序提供了一种称为实时 (JIT) 访问的功能，该功能目前为预览版。

JIT 访问允许你请求对托管应用程序资源的提升访问权限，以便进行故障排除或维护。 你始终对资源具有只读访问权限，但在特定的时间段内，你可以具有更高的访问权限。

用于授予访问权限的工作流为：

1. 你将托管应用程序添加到市场，并指定 JIT 访问可用。

1. 在部署过程中，使用者为托管应用程序的实例启用 JIT 访问。

1. 在部署后，使用者可以更改 JIT 访问设置。

1. 需要对托管资源进行故障排除或更新时，你发送访问请求。

1. 使用者批准你的请求。

本文重点介绍了发布者在启用 JIT 访问和提交请求时要执行的操作。 若要了解如何批准 JIT 访问请求，请参阅[在 Azure 托管应用程序中批准实时访问](approve-just-in-time-access.md)。

## <a name="add-jit-access-step-to-ui"></a>向 UI 添加 JIT 访问步骤

除了必须包括一个步骤来让使用者启用 JIT 访问之外，CreateUiDefinition.json 文件与你为永久访问创建的 UI 文件完全相同。 若要详细了解如何在 Azure 市场中发布你的第一个托管应用程序产品/服务，请参阅[市场中的 Azure 托管应用程序](publish-marketplace-app.md)。

若要让你的产品/服务支持 JIT 功能，请将以下内容添加到 CreateUiDefinition.json 文件：

在 "steps" 中：

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
 
在 "outputs" 中：

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JIT 访问功能目前为预览版。 JIT 配置的架构在将来的迭代中可能会更改。

## <a name="enable-jit-access"></a>启用 JIT 访问

在市场中定义产品/服务时，请确保启用 JIT 访问。

1. 登录到[云合作伙伴发布门户](https://cloudpartner.azure.com)。

1. 提供相应的值以在市场中发布托管应用程序。 对于“启用 JIT 访问?”，选择“是”。  

   ![启用实时访问](./media/request-just-in-time-access/marketplace-enable.png)

你已向 UI 添加了一个 JIT 配置步骤，并在市场产品/服务中启用了 JIT 访问。 当使用者部署你的托管应用程序时，他们可以[为其实例启用 JIT 访问](approve-just-in-time-access.md#enable-during-deployment)。

## <a name="request-access"></a>请求访问权限

当你需要访问使用者的托管资源时，你发送一个请求来请求特定的角色、时间和持续时间。 然后，使用者必须批准该请求。

若要发送 JIT 访问请求，请执行以下操作：

1. 针对你需要访问的托管应用程序选择“JIT 访问”  。

1. 选择“符合条件的角色”  ，然后针对你需要的角色在“操作”列中选择“激活”  。

   ![激活访问请求](./media/request-just-in-time-access/send-request.png)

1. 在“激活角色”  窗体上，选择要激活的角色的开始时间和持续时间。 选择“激活”  以发送请求。

   ![激活访问权限](./media/request-just-in-time-access/activate-access.png) 

1. 查看通知，以查看是否已成功将新的 JIT 请求发送到使用者。

   ![通知](./media/request-just-in-time-access/in-progress.png)

   现在，你必须等待使用者[批准你的请求](approve-just-in-time-access.md#approve-requests)。

1. 若要查看对托管应用程序的所有 JIT 请求的状态，请依次选择“JIT 访问”  和“请求历史记录”  。

   ![查看状态](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>已知问题

必须在托管应用程序定义中显式包括请求 JIT 访问的帐户的主体 ID。 不能仅通过在包中指定的组来包括该帐户。 将来的版本会纠正此限制。

## <a name="next-steps"></a>后续步骤

若要了解如何批准 JIT 访问请求，请参阅[在 Azure 托管应用程序中批准实时访问](approve-just-in-time-access.md)。
