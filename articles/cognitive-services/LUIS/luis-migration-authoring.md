---
title: 迁移到 Azure 资源进行创作
titleSuffix: Azure Cognitive Services
description: 迁移到 Azure 创作资源密钥。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: ec6f9592a4c149be382fab66cca27d929644d988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194503"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>迁移到 Azure 资源创作密钥

语言理解 （LUIS） 创作身份验证从电子邮件帐户更改为 Azure 资源。 虽然当前不需要，但将来将强制执行切换到 Azure 资源。

## <a name="why-migrate"></a>为何要迁移？

使用 Azure 资源进行创作允许您作为资源的所有者来控制对创作的访问。 您可以创建创作资源并将其命名为创作资源，以管理不同的作者组。

例如，您是 2 个 LUIS 应用的所有者，并且每个应用都有不同的成员是协作者。 您可以创建两个不同的创作资源，并将每个应用分配给每个单独的资源。 然后，根据每个成员协作使用的应用，将每个成员分配为适当创作资源的参与者。 Azure 创作资源控制授权。

> [!Note]
> 在迁移之前，共同作者称为 LUIS 应用级别的_协作者_。 迁移后，_参与者_的 Azure 角色用于相同的功能，但在 Azure 资源级别上。

## <a name="what-is-migrating"></a>什么是迁移？

迁移包括：

* LUIS 的所有用户、所有者和贡献者。
* **所有**应用。
* **单向**迁移。

所有者无法选择要迁移的应用子集，并且该过程不可逆。

迁移不是：

* 收集协作者并自动移动或添加到 Azure 创作资源的过程。 作为应用所有者，您需要完成此步骤。 此步骤需要对相应资源的权限。
* 创建和分配预测运行时资源的过程。 如果需要预测运行时资源，这是[一个单独的进程](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)，并且保持不变。

## <a name="how-are-the-apps-migrating"></a>应用程序如何迁移？

[LUIS 门户](https://www.luis.ai)提供迁移过程。

如果：

* 电子邮件身份验证系统上有用于创作的应用。
* 您是应用程序所有者。

您可以通过取消窗口来延迟迁移过程。 定期要求您迁移，直到迁移或迁移截止时间过去。 可以从顶部导航栏的锁定图标开始迁移过程。

## <a name="migration-for-the-app-owner"></a>应用所有者的迁移

### <a name="before-you-migrate"></a>迁移之前

* **必需**，您需要具有[Azure 订阅](https://azure.microsoft.com/free/)。 订阅过程的一部分确实需要计费信息。 但是，您可以使用 LUIS 时的免费`F0`（ ） 定价层。
* **或者，** 通过导出每个应用或使用导出[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)从 LUIS 门户的应用列表中备份应用。
* **可以选择**，保存每个应用程序的协作者列表。 此电子邮件列表作为迁移过程的一部分提供。


**创作您的 LUIS 应用程序是免费的**，由`F0`层指示。 [了解有关定价层的更多。](luis-boundaries.md#key-limits)

如果没有 Azure 订阅，[请注册](https://azure.microsoft.com/free/)。

### <a name="migration-steps"></a>迁移步骤

按照[这些迁移步骤](luis-migration-authoring-steps.md)操作。

### <a name="after-you-migrate"></a>迁移后

迁移过程后，所有 LUIS 应用现在都分配给单个 LUIS 创作资源。

您可以创建更多创作资源，并从_LUIS 门户_中的 **"管理-> Azure 资源**"页进行分配。

您可以在该资源的**访问控制 （IAM）** 页上从_Azure 门户_向创作资源添加参与者。 有关详细信息，请参阅[添加参与者访问权限](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)。

|门户|目的|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|• 创建预测和创作资源。<br>• 分配贡献者。|
|[路易斯](https://www.luis.ai)|• 迁移到新的创作资源。<br>• 从**管理 -> Azure 资源**页分配或取消分配预测和创作资源到应用。|

## <a name="migration-for-the-app-contributor"></a>应用参与者的迁移

LUIS 的每个用户都需要迁移，包括协作者/贡献者。 协作者必须迁移才能访问应用。

> [!Note]
> 如果 LUIS 应用的所有者迁移并添加了协作者作为 Azure 资源上的参与者，则协作者仍无权访问该应用，除非它们也迁移。

### <a name="before-the-app-is-migrated"></a>迁移应用之前

您可以选择导出您是协作者的应用，然后将应用导入 LUIS。 导入过程创建具有新应用 ID 的新应用，您是该应用 ID 的所有者。

### <a name="after-the-app-is-migrated"></a>迁移应用后

应用所有者需要[将电子邮件添加到 Azure 创作资源作为协作者](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)。

迁移过程后，您拥有的任何应用都可以在 LUIS 门户的"**我的应用"** 页面上使用。

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>为 LUIS 创作解决迁移过程的疑难解答

* LUIS 创作密钥仅在迁移过程完成后在 LUIS 门户中可见。 如果创建创作密钥（例如使用 LUIS CLI），则用户仍然需要在 LUIS 门户中完成迁移过程。
* 如果迁移的用户在其 Azure 资源上添加非迁移用户作为参与者，则非迁移用户将无权访问应用，除非它们迁移。
* 如果非迁移用户不是任何应用的所有者，而是其他人拥有的其他应用的协作者，并且所有者已经历迁移过程，则此用户将需要迁移才能访问这些应用。
* 如果非迁移用户将另一个迁移用户作为协作者添加到其应用，则会发生错误，因为您将无法将迁移的用户作为协作者添加到应用。 然后，非迁移用户必须完成迁移过程并创建 Azure 资源，并将迁移的用户添加为该资源的参与者。

如果在以下情况：以下情况，则在迁移过程中收到错误：
* 您的订阅不授权您创建认知服务资源
* 您的迁移会对任何应用程序运行时产生负面影响。 迁移时，任何协作者都将从你的应用中删除，您将从其他应用中删除作为协作者。 此过程意味着您分配的密钥也会被删除。 如果您在其他应用中分配了密钥，则迁移将被阻止。 在迁移之前，请安全地删除您分配的密钥。 如果您知道分配的密钥在运行时未使用，则需要将其删除才能在迁移中进行。

使用以下 URL 格式访问应用的 Azure 资源列表：

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>后续步骤

* [如何将应用迁移到创作资源](luis-migration-authoring-steps.md)
