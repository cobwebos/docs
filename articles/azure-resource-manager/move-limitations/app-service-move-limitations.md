---
title: 移动 Azure App Service 资源
description: 使用 Azure 资源管理器将应用服务资源移到新的资源组或订阅。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 04ddf644c58434531dde708ee3b6432b1fce8f91
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533578"
---
# <a name="move-guidance-for-app-service-resources"></a>应用服务资源的移动指南

移动应用服务资源的步骤会有所不同，这取决于你是要将资源移动到订阅中，还是移动到新的订阅。

## <a name="move-in-same-subscription"></a>在同一订阅中移动

在同一订阅中移动 Web 应用时，无法移动第三方 SSL 证书。 不过，可以将 Web 应用移动到新的资源组而不移动其第三方证书，并且，应用的 SSL 功能仍然可以工作。

如果希望随 Web 应用移动 SSL 证书，请执行以下步骤：

1. 从 Web 应用中删除第三方证书，但保留证书的副本
2. 移动 Web 应用。
3. 将第三方证书上传到移动后的 Web 应用。

## <a name="move-across-subscriptions"></a>跨订阅移动

_在订阅之间_移动 Web 应用时存在以下限制：

- 目标资源组中不能有任何现有的应用服务资源。 应用服务资源包括：
    - Web 应用
    - 应用服务计划
    - 上传或导入的 SSL 证书
    - 应用服务环境
- 资源组中的所有应用服务资源必须一起移动。
- 只能从最初创建应用服务资源的资源组中移动它们。 如果应用服务资源不再在其原始资源组中，请将其移回其原始资源组。 然后，在订阅之间移动资源。

如果不记得原始资源组，可以通过诊断来查找。 对于 web 应用，请选择 "**诊断并解决问题**"。 然后选择 "**配置和管理**"。

![选择诊断](./media/app-service-move-limitations/select-diagnostics.png)

选择 "**迁移选项**"。

![选择迁移选项](./media/app-service-move-limitations/select-migration.png)

选择用于移动 web 应用的建议步骤的选项。

![选择建议步骤](./media/app-service-move-limitations/recommended-steps.png)

在移动资源之前，你会看到建议采取的操作。 此信息包括 web 应用的原始资源组。

![建议](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>移动应用服务证书

可将应用服务证书移动到新的资源组或订阅。 如果应用服务证书已绑定到某个 Web 应用，必须先执行一些步骤，然后才能将资源移到新的订阅中。 移动资源之前，请在 Web 应用中删除 SSL 绑定和专用证书。 应用服务证书不需删除，只需删除 Web 应用中的专用证书。

## <a name="move-support"></a>移动支持

若要确定可移动的应用服务资源，请参阅：

- [AppService](../move-support-resources.md#microsoftappservice)
- [CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>后续步骤

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](../resource-group-move-resources.md)。
