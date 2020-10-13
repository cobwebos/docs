---
title: 迁移到 Azure 资源创作密钥
titleSuffix: Azure Cognitive Services
description: 本文介绍如何将语言理解 (LUIS) 创作身份验证从电子邮件帐户迁移到 Azure 资源。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 926b79e672c14249ec7c2b053dba7eb3a31443a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536031"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>迁移到 Azure 资源创作密钥

语言理解 (LUIS) 创作身份验证已从电子邮件帐户更改为 Azure 资源。 虽然目前不需要切换到 Azure 资源，但将来会强制执行此功能。


## <a name="what-is-migration"></a>什么是迁移？

迁移是将创作身份验证从电子邮件帐户更改为 Azure 资源的过程。 迁移后，你的帐户将链接到 Azure 订阅和 Azure 创作资源。 *最终需要迁移 (所有者或协作者) 的所有 LUIS 用户。*

必须从 LUIS 门户完成迁移。 例如，如果使用 LUIS CLI 创建创作密钥，则需要在 LUIS 门户中完成迁移过程。 迁移后，你仍可以对应用程序进行 coauthors，但这些将添加到 Azure 资源级别，而不是应用程序级别。

> [!Note]
> 在迁移之前，coauthors 称为 "LUIS" 应用级别的 _协作_ 者。 迁移后， _参与者_ 的 azure 角色用于 azure 资源级别的相同功能。

## <a name="note-before-you-migrate"></a>请注意，在迁移之前

* 必须按 **11 月2日（2020）** 迁移创作体验。 
* 迁移是一个单向过程。 迁移后无法返回。
* 如果你是应用程序的所有者，应用程序将自动与你一起迁移。
* 所有者无法选择要迁移的应用子集，此过程不可逆。
* 所有者迁移后，应用程序将从协作者的一侧消失。
* 系统会提示所有者向协作者发送电子邮件，告知他们迁移。
* 如果你是应用程序的协作者，应用程序将不会迁移。
* 所有者无法知道协作者已迁移。
* 迁移不会自动收集协作者，也不会将其移动或添加到 Azure 创作资源。 应用所有者是在迁移后需要完成此步骤的人员。 此步骤需要 [Azure 创作资源的权限](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate)。
* 在将协作者分配到 Azure 资源后，他们需要迁移以访问应用程序。 否则，他们将无权使用创作应用程序。
* 无法将迁移的用户添加为应用程序的协作者。
* 如果您拥有分配给其他用户所拥有的应用程序的预测密钥，这会阻止所有者和协作者进行迁移。 请参阅本文后面的建议。

> [!Note]
> 如果需要创建预测运行时资源，有 [一个单独的进程](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 来创建它。

## <a name="migration-prerequisites"></a>迁移先决条件

* 需要与有效的 Azure 订阅相关联。 要求租户管理员将你添加到订阅，或者注册一个 [免费](https://azure.microsoft.com/free/cognitive-services)订阅。
* 需要从 LUIS 门户或 Azure 门户创建 LUIS Azure 创作资源。 从 LUIS 门户创建创作资源是下一节中讨论的迁移流程的一部分。
* 如果你是应用程序的协作者，应用程序将不会自动迁移。 建议你通过导出或使用 [导出 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)来备份这些应用程序。 迁移后，可以将应用导入回 LUIS。 导入过程使用新的应用 ID 创建新应用，你是其所有者。
* 如果你是应用程序的所有者，则无需导出应用，因为它们会自动迁移。 建议保存每个应用的协作者列表。 提供此列表的电子邮件模板在迁移过程中是可选的。


|门户|目的|
|--|--|
|[Azure](https://azure.microsoft.com/free/cognitive-services)| 创建预测和创作资源。<br> 分配资源的参与者。|
|[LUIS](https://www.luis.ai)| 迁移到新的创作资源。<br> 在迁移流程中创建新的创作资源。<br> 从 "**管理**  >  **Azure 资源**" 页为应用分配或取消分配预测和创作资源。 <br> 将应用程序从一个创作资源移动到另一个创作资源。  |

> [!Note]
> LUIS 应用免费创作，如 F0 层所示。 了解[有关定价层的更多信息](luis-limits.md#key-limits)。


## <a name="migration-steps"></a>迁移步骤

1. 在正在使用的 LUIS 门户中，可以从顶部工具栏上的 **Azure** 图标开始迁移过程。

   > [!div class="mx-imgBorder"]
   > ![“迁移”图标](./media/migrate-authoring-key/migration-button.png)

2. 使用“迁移”弹出窗口可以继续迁移或稍后进行迁移。 选择“立即迁移”。

   > [!div class="mx-imgBorder"]
   > ![迁移过程中的第一个弹出窗口，可在其中选择 "立即迁移"](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. 如果任何应用有协作者，系统将提示你向他们发送一封电子邮件，让他们了解迁移。 这是可选步骤。

   对于每个协作者和应用，会打开默认的电子邮件应用程序，显示一封格式简单的电子邮件。 可以在发送电子邮件之前对其进行编辑。 电子邮件模板包含确切的应用 ID 和应用名称。

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```

   > [!Note]
   > 将你的帐户迁移到 Azure 后，你的应用程序将不能再用于协作者。

4. 如果你是任何应用程序的协作者，系统将提示你在迁移流程中通过选择此选项来导出应用的副本。 这是可选步骤。

   如果选择该选项，将显示以下页。 选择左侧的 "下载" 按钮以导出所需的应用。 你可以在迁移后重新导入这些应用，因为它们不会随你自动迁移。

   > [!div class="mx-imgBorder"]
   > ![提示导出应用程序。](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. 如果已从 Azure 创建新的 LUIS 创作资源，可以选择创建新的创作资源或迁移到现有创作资源。 选择以下按钮之一，选择所需的选项。

   > [!div class="mx-imgBorder"]
   > ![用于创建新创作资源和使用现有创作资源的按钮](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>从 LUIS 创建新的创作资源以进行迁移

如果要创建新的创作资源，请选择 " **创建新的创作资源** "，并在下一个窗口中提供以下信息。 然后选择“完成”。

> [!div class="mx-imgBorder"]
> ![用于创建创作资源的窗口](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **租户名称**： Azure 订阅与之关联的租户。 默认情况下，此值设置为当前正在使用的租户。 可以通过选择最右侧的头像（其中包含你的姓名）来切换租户。
* **资源名称**：所选的自定义名称。 它用作创作和预测终结点查询的 URL 的一部分。
* **订阅名称**：将与资源关联的订阅。 如果有多个订阅属于租户，请从下拉列表中选择所需的订阅。
* **Azure 资源组名称**：从下拉列表中选择的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。

请注意，每个订阅的每个区域可以有10个免费创作资源。 如果订阅的同一区域中的创作资源超过10个，将无法创建新的资源。

创建了创作资源后，会显示成功消息。 选择“关闭”以关闭弹出窗口。

  > [!div class="mx-imgBorder"]
  > ![表明创作资源已成功创建的消息](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>使用现有的创作资源进行迁移

如果你的订阅已与 LUIS 创作 Azure 资源关联，或者你已从 Azure 门户创建了一个资源，并且想要迁移到该资源，而不是创建一个新资源，请选择 " **使用现有的创作资源**"。 在下一个窗口中提供以下信息，然后选择 " **完成**"。

> [!div class="mx-imgBorder"]
>![用于更改现有创作资源的窗口](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **租户名称**： Azure 订阅与之关联的租户。 默认情况下，此值设置为当前正在使用的租户。
* **订阅名称**：将与资源关联的订阅。 如果有多个订阅属于租户，请从下拉列表中选择所需的订阅。
* **资源名称**：要迁移到的创作资源。

> [!Note]
> 如果在下拉列表中看不到你的创作资源，请确保根据你登录的 LUIS 门户在适当的位置创建它。 此外，请确保创建的是创作资源，而不是预测资源。


验证你的创作资源名称并选择 " **迁移** " 按钮。

> [!div class="mx-imgBorder"]
> ![用于选择创作资源的窗口，现在可以使用 "迁移" 按钮](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

一条成功消息随即出现。 选择“关闭”以关闭弹出窗口。

> [!div class="mx-imgBorder"]
> ![表明创作资源已成功迁移的消息](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>迁移后使用应用

迁移过程完成后，你作为其所有者的所有 LUIS 应用将立即分配给单个 LUIS 创作资源。

" **我的应用** " 列表显示迁移到新的创作资源的应用。 在访问你的应用程序之前，请选择 "订阅" 和 "LUIS 创作资源"，以查看可以创作的应用。

 > [!div class="mx-imgBorder"]
 > ![订阅和创作资源的框](./media/create-app-in-portal-select-subscription-luis-resource.png)

无需知道创作资源的密钥即可继续在 LUIS 门户中编辑应用。

如果计划以编程方式编辑应用，则需要创作密钥值。 这些值显示在 LUIS 门户中的 "**管理**  >  **Azure 资源**" 页上。 资源的 " **密钥** " 页上的 "Azure 门户中也提供了它们。 你还可以创建更多的创作资源并从同一页分配这些资源。

 > [!div class="mx-imgBorder"]
 > ![用于管理创作资源的页面](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>添加参与者以创作资源

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

了解如何在创作资源上 [添加参与者](luis-how-to-collaborate.md) 。 参与者将有权访问该资源下的所有应用程序。

可以在该资源的 " **访问控制" (IAM ") ** 页上，将参与者添加到 Azure 门户中的创作资源。 有关详细信息，请参阅 [添加参与者访问](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)。

> [!Note]
> 如果 LUIS 应用的所有者将协作者迁移到并添加为 Azure 资源上的参与者，协作者将仍无法访问该应用，除非他们自己也进行迁移。

## <a name="luis-portal-migration-reminders"></a>LUIS 门户迁移提醒

[LUIS 门户](https://www.luis.ai)提供迁移过程。

如果满足以下两个条件，系统会要求你进行迁移：
* 在电子邮件身份验证系统上有用于创作的应用。
* 你是应用所有者。

每周一次，系统都将提示您迁移应用程序。 无需迁移即可关闭此窗口。 如果要在下一个计划的时间段之前迁移，可以从 LUIS 门户顶部工具栏上的 **Azure** 图标开始迁移过程。

## <a name="prediction-resources-blocking-migration"></a>阻止迁移的预测资源
迁移会对任何应用程序的运行时产生负面影响。 迁移时，会从应用中删除任何协作者，并将其作为协作者从其他应用中删除。 此过程意味着还会删除合作者分配的密钥，如果应用程序在生产环境中，则可能会破坏应用程序。 这就是我们阻止迁移的原因，直到你手动删除分配给他们的协作者或密钥。

如果满足以下任一条件，则会阻止迁移：

* 您已在不属于的应用程序中分配了预测/运行时资源。
* 你有其他用户向你拥有的应用程序分配预测/运行时资源。

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>如果你是应用的所有者，则为建议步骤
如果你是某些应用程序的所有者，并且你有协作者为这些应用程序分配了预测/运行时密钥，则在迁移时，将出现错误。 此错误列出了分配给其他用户的预测密钥的应用程序 Id。

建议：
* 通知协作者有关迁移的信息。
* 从错误中显示的应用程序中删除所有协作者。
* 执行迁移过程，如果手动删除协作者，应会成功。
* 为新的创作资源分配合作者作为参与者。 协作者将预测资源迁移并重新分配给应用程序。 请注意，这将导致暂时中断应用程序，直到重新分配预测资源。

这里有另一个可能的解决方案。 在所有者迁移之前，协作者可以在 Azure 门户的 Azure 订阅中添加应用程序所有者作为参与者。 此步骤将授予所有者对运行时预测资源的访问权限。 如果所有者通过使用新的订阅进行迁移，则会将其添加到 (在新租户) 下，此步骤不会立即取消阻止协作者和应用所有者的迁移过程。 它还允许对应用进行平滑迁移，并为其分配预测密钥，而不会中断应用。


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>如果你是应用的协作者，建议的步骤
如果要在应用程序上进行协作，并且已为这些应用程序分配了预测/运行时密钥，则在迁移时，将出现错误。 此错误列出了阻止迁移的应用程序 Id 和密钥路径。

建议：
* 将应用程序导出为备份。 这是迁移过程中的可选步骤。
* 从**管理**  >  **Azure 资源**页取消分配预测资源。
* 迁移过程。
* 迁移后导入后导入应用程序。
* 从 "**管理**  >  **Azure 资源**" 页将预测密钥重新分配给应用程序。

> [!Note]
> 当你在迁移后导入应用程序时，它们将具有不同的应用程序 Id。 它们还不同于在生产中命中的用户。 你现在将是这些应用程序的所有者。

## <a name="troubleshooting-the-migration-process"></a>迁移过程疑难解答

尝试在下拉列表中进行迁移但找不到 Azure 订阅时：
* 确保你有一个有效的 Azure 订阅，该订阅有权创建认知服务资源。 中转到 [Azure 门户](https://ms.portal.azure.com) 并检查订阅的状态。 如果没有，请 [创建一个免费的 Azure 帐户](https://azure.microsoft.com/free/cognitive-services/)。
* 确保你处于与有效订阅关联的正确租户中。 你可以在此工具栏上将租户从你姓名首字母的左侧的头像切换到你 ![ 可以切换租户的工具栏](./media/migrate-authoring-key/switch-user-tenant-2.png)

如果有现成的创作资源，但在选择 " **使用现有创作资源** " 选项时找不到该资源：
* 你的资源可能在不同于你登录的门户的位置创建。 检查 [LUIS 创作区域和门户](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)。
* 改为通过 LUIS 门户创建新资源。

如果选择 " **创建新的创作资源** " 选项，并且迁移失败，并出现错误消息 "检索用户的 Azure 信息失败，请稍后重试"：
* 对于每个区域，订阅可能有10个或更多的创作资源，每个订阅。 如果是这种情况，您将无法创建新的创作资源。
* 通过选择 " **使用现有创作资源** " 选项并选择订阅下的现有资源之一来进行迁移。

如果你看到以下错误，请检查 [建议的步骤（如果你是该应用程序的所有者](#recommended-steps-if-youre-the-owner-of-the-app)）。
![显示所有者的迁移失败的错误](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

如果你看到以下错误，请检查 [建议的步骤（如果你是应用的协作](#recommended-steps-if-youre-a-collaborator-on-an-app)者）。
![为协作者显示迁移失败的错误](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>后续步骤

* 查看 [有关创作和运行时密钥的概念](luis-how-to-azure-subscription.md)。
* 查看如何 [分配密钥](luis-how-to-azure-subscription.md) 并 [添加参与者](luis-how-to-collaborate.md)。
