---
title: 移动 Azure App Service 资源
description: 使用 Azure 资源管理器将应用服务资源移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 338b0559a5de9468ff60024b88d0f676a9fc3e8b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479795"
---
# <a name="move-guidance-for-app-service-resources"></a>应用服务资源的移动指南

本文介绍了移动应用服务资源的步骤。 将应用服务资源移动到新的订阅有一些具体的要求。

## <a name="move-across-subscriptions"></a>跨订阅移动

跨订阅移动 Web 应用时，以下指南适用：

- 目标资源组中不能有任何现有的应用服务资源。 应用服务资源包括：
    - Web 应用
    - 应用服务计划
    - 上传或导入的 SSL 证书
    - 应用服务环境
- 资源组中的所有应用服务资源必须一起移动。 请注意，应用服务环境不能移动到新的资源组，也不能移动到新的订阅。
- 只要将证书与资源组中的所有其他资源一起移动，就可以移动绑定到 web 的证书而无需删除 SSL 绑定。
- 只能从最初创建应用服务资源的资源组中移动它们。 如果应用服务资源不再在其原始资源组中，请将其移回其原始资源组。 然后，在订阅之间移动资源。

如果不记得原始资源组，可以通过诊断来查找。 对于 web 应用，请选择 "**诊断并解决问题**"。 然后选择 "**配置和管理**"。

![选择诊断](./media/app-service-move-limitations/select-diagnostics.png)

选择 "**迁移选项**"。

![选择迁移选项](./media/app-service-move-limitations/select-migration.png)

选择用于移动 web 应用的建议步骤的选项。

![选择建议步骤](./media/app-service-move-limitations/recommended-steps.png)

在移动资源之前，你会看到建议采取的操作。 此信息包括 web 应用的原始资源组。

![建议](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>移动支持

若要确定可移动的应用服务资源，请参阅：

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>后续步骤

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](../move-resource-group-and-subscription.md)。
