---
title: 迁移到 Azure 创作资源
titleSuffix: Azure Cognitive Services
description: 迁移到 Azure 创作资源。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: f5197ae79670e4543c58224a33838706edae6218
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194626"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>迁移到 Azure 创作资源的步骤

从语言理解 （LUIS） 门户中，迁移您拥有的所有应用以使用 Azure 创作资源。

## <a name="prerequisites"></a>先决条件

* **或者，** 通过导出每个应用或使用导出[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)从 LUIS 门户的应用列表中备份应用。
* **可以选择**，保存每个应用程序的协作者列表。 作为迁移过程的一部分，可以向所有协作者发送电子邮件。
* **必需**，您需要具有[Azure 订阅](https://azure.microsoft.com/free/)。 订阅过程的一部分确实需要计费信息。 但是，当您使用 LUIS 时，可以使用免费 （F0） 定价层。 随着使用量的增加，您最终可能会发现您需要付费层。

如果没有 Azure 订阅，[请注册](https://azure.microsoft.com/free/)。

## <a name="access-the-migration-process"></a>访问迁移过程

每周都会提示您迁移应用。 无需迁移即可取消此窗口。 如果要在下一个计划期间之前迁移，可以从 LUIS 门户顶部工具栏上的**Azure**图标开始迁移过程。

> [!div class="mx-imgBorder"]
> ![迁移图标](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>应用所有者开始迁移过程

如果您是任何 LUIS 应用的所有者，则迁移过程可用。

1. 登录 [LUIS 门户](https://www.luis.ai)并同意使用条款。
1. 迁移弹出窗口允许您继续迁移或以后迁移。 选择 **"立即迁移**"。 如果选择以后迁移，则有 9 个月的时间迁移到 Azure 中的新创作密钥。

    ![迁移过程中的第一个弹出窗口，选择"立即迁移"。](./media/migrate-authoring-key/migrate-now.png)

1. 或者，如果任何应用都有协作者，系统会提示他们**发送电子邮件**，让他们知道迁移。 这是可选步骤。

    将帐户迁移到 Azure 后，您的应用将不再可供协作者使用。

    对于每个协作者和应用程序，默认电子邮件应用程序将打开，并打开一封格式稍浅的电子邮件。 您可以在发送电子邮件之前对其进行编辑。

    电子邮件模板包括确切的应用 ID 和应用名称。

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. 选择通过选择使用现有创作资源或创建新的创作资源来创建 LUIS 创作资源。

    > [!div class="mx-imgBorder"]
    > ![创建创作资源](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. 在下一个窗口中，输入资源密钥信息。 输入信息后，选择 **"创建资源**"。 每个订阅可以拥有 10 个免费创作资源。

    ![创建创作资源](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    **创建新的创作资源**时，请提供以下信息：

    * **资源名称** - 你选择的自定义名称，用作创作和预测终结点查询的 URL 的一部分。
    * **租户** - 与 Azure 订阅关联的租户。
    * **订阅名称** - 将对资源计费的订阅。
    * **资源组** - 你选择或创建的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。
    * **位置** - 位置选项基于**资源组**选择。
    * **定价层** - 定价层确定每秒和每月的最大事务数。

1. 验证您的创作资源，**然后立即迁移**。

    ![创建创作资源](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. 创建创作资源时，将显示成功消息。 选择 **"关闭"** 以关闭弹出窗口。

    ![已成功创建创作资源。](./media/migrate-authoring-key/migration-success.png)

    "**我的应用"** 列表显示迁移到新创作资源的应用。

    在 LUIS 门户中继续编辑应用不需要知道创作资源的密钥。 如果您计划以编程方式编辑应用，则需要创作键值。 这些值显示在 LUIS 门户中的 **"管理-> Azure 资源**"页上，并且在资源 **"密钥"** 页上的 Azure 门户中也可用。

1. 在访问应用之前，请选择订阅和 LUIS 创作资源以查看可以创作的应用。

    ![选择订阅和 LUIS 创作资源以查看可以创作的应用。](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>应用参与者开始迁移过程

按照与应用所有者相同的步骤进行迁移。 该过程创建了一种新的类型`LUIS.Authoring`创作资源。

您需要迁移您的帐户，以便将其添加为其他人拥有的迁移应用的贡献者。

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>迁移过程后，向创作资源添加参与者

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

[了解如何添加参与者](luis-how-to-collaborate.md)。

## <a name="troubleshooting-errors-with-the-migration-process"></a>通过迁移过程排除错误

如果在迁移过程中在`MissingSubscriptionRegistration`LUIS 门户中收到带有红色通知栏的错误，请在[Azure 门户](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)或[Azure CLI](luis-how-to-azure-subscription.md#create-resources-in-azure-cli)中创建认知服务资源。 了解有关[此错误原因](../../azure-resource-manager/templates/error-register-resource-provider.md#cause)的详细信息。

## <a name="next-steps"></a>后续步骤


* 查看有关创作和运行时密钥[的概念](luis-concept-keys.md)
* 查看[如何分配密钥](luis-how-to-azure-subscription.md)和添加[参与者](luis-how-to-collaborate.md)
