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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194503"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>迁移到 Azure 资源创作密钥

语言理解（LUIS）创作身份验证从电子邮件帐户更改为 Azure 资源。 目前不需要切换到 Azure 资源，以后将强制实施。

## <a name="why-migrate"></a>为何要迁移？

使用 Azure 资源进行创作可以让你作为资源的所有者来控制对创作的访问。 你可以创建和命名创作资源，以管理不同的作者组。

例如，你是2个 LUIS 应用的所有者，并且你有不同的成员，这些成员是每个应用的协作者。 可以创建两个不同的创作资源，并将每个应用程序分配给每个单独的资源。 然后，将每个成员作为参与者分配给适当的创作资源，具体取决于他们所在的应用。 Azure 创作资源控制授权。

> [!Note]
> 在迁移之前，共同创作者称为 LUIS 应用级别的_协作_者。 迁移后，_参与者_的 azure 角色用于相同的功能，但在 azure 资源级别上使用。

## <a name="what-is-migrating"></a>什么是迁移？

迁移包括：

* LUIS、所有者和参与者的所有用户。
* **所有**应用。
* 单向**迁移。**

所有者无法选择要迁移的应用子集，此过程不可逆。

迁移不是：

* 收集协作者并自动移动或添加到 Azure 创作资源的进程。 作为应用所有者，需要完成此步骤。 此步骤需要具有相应资源的权限。
* 用于创建和分配预测运行时资源的进程。 如果需要预测运行时资源，这是[一个单独的进程](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)，并且保持不变。

## <a name="how-are-the-apps-migrating"></a>如何迁移应用？

[LUIS 门户](https://www.luis.ai)提供迁移过程。

系统会要求你在以下情况中进行迁移：

* 你具有用于创作的电子邮件身份验证系统上的应用。
* 你是应用所有者。

您可以通过取消窗口来延迟迁移过程。 系统会定期要求你迁移，直到你迁移或通过迁移截止时间。 可以从顶部导航栏的锁定图标开始迁移过程。

## <a name="migration-for-the-app-owner"></a>应用所有者的迁移

### <a name="before-you-migrate"></a>迁移之前

* **需要，需要**有一个[Azure 订阅](https://azure.microsoft.com/free/)。 订阅过程的一部分需要计费信息。 但是，在使用 LUIS 时，可以使用免费（`F0`）定价层。
* **（可选**）通过导出每个应用或使用导出[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)，从 LUIS 门户的 "应用" 列表备份应用。
* **还可以选择**保存每个应用的 collaborator's 列表。 此电子邮件列表作为迁移过程的一部分提供。


**创作你的 LUIS 应用程序是免费**的，由 `F0` 层指示。 了解[有关定价层的详细信息](luis-boundaries.md#key-limits)。

如果你没有 Azure 订阅，请[注册](https://azure.microsoft.com/free/)。

### <a name="migration-steps"></a>迁移步骤

请按照[以下迁移步骤](luis-migration-authoring-steps.md)操作。

### <a name="after-you-migrate"></a>迁移后

迁移过程完成后，所有 LUIS 应用现已分配给单个 LUIS 创作资源。

你可以在_LUIS 门户_中创建更多的创作资源，并从**管理-> Azure 资源**页分配。

您可以在该资源的 "**访问控制（IAM）** " 页上，将参与者添加到_Azure 门户_中的创作资源。 有关详细信息，请参阅[添加参与者访问](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)。

|门户|目的|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* 创建预测和创作资源。<br>* 分配参与者。|
|[LUIS](https://www.luis.ai)|* 迁移到新的创作资源。<br>* 通过 "**管理-> Azure 资源**" 页为应用分配预测和创作资源。|

## <a name="migration-for-the-app-contributor"></a>应用参与者的迁移

LUIS 的每个用户都需要迁移，包括协作者/参与者。 协作者必须迁移才能访问应用。

> [!Note]
> 如果 LUIS 应用的所有者迁移并添加了协作者作为 Azure 资源的参与者，则协作者仍将无法访问应用，除非他们还在迁移。

### <a name="before-the-app-is-migrated"></a>迁移应用之前

您可以选择导出您是其协作者的应用程序，然后将该应用程序导回 LUIS。 导入过程将创建一个新应用，其中包含你作为其所有者的新应用 ID。

### <a name="after-the-app-is-migrated"></a>迁移应用后

应用所有者需要[将电子邮件作为协作者添加到 Azure 创作资源](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)。

迁移过程完成后，LUIS 门户的 "**我的应用**" 页上提供了你拥有的任何应用。

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>排查 LUIS 创作的迁移过程

* 迁移过程完成后，LUIS 创作密钥仅在 LUIS 门户中可见。 如果创建了创作密钥，例如 with LUIS CLI，则用户仍需在 LUIS 门户中完成迁移过程。
* 如果已迁移的用户在其 azure 资源上添加了非迁移用户作为参与者，则未迁移的用户将无法访问应用，除非他们迁移。
* 如果非迁移的用户不是任何应用的所有者，而是其他用户拥有的其他应用的协作者，且这些所有者已完成迁移过程，则此用户需要迁移才能访问应用。
* 如果非迁移的用户将其他已迁移用户添加为其应用的协作者，则会发生错误，因为无法将迁移的用户作为协作者添加到应用中。 然后，非迁移的用户必须完成迁移过程，并创建 azure 资源并将迁移的用户作为参与者添加到该资源。

如果出现下列情况，则在迁移过程中会收到错误：
* 你的订阅不会授权你创建认知服务资源
* 迁移会对任何应用程序运行时产生负面影响。 迁移时，会从应用中删除任何协作者，并将其作为协作者从其他应用中删除。 此过程表明，分配的密钥也会删除。 如果已在其他应用中分配密钥，则迁移将被阻止。 在迁移之前，请删除安全指定的密钥。 如果你知道在运行时中未使用分配的密钥，则需要将其删除才能在迁移过程中进行。

使用以下 URL 格式访问应用的 Azure 资源列表：

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>后续步骤

* [如何将应用迁移到创作资源](luis-migration-authoring-steps.md)
