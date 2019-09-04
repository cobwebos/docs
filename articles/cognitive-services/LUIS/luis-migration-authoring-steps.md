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
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 13eb301daadb70456c0c0d7db8991d7a695b1de9
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259680"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>迁移到 Azure 创作资源的步骤

从语言理解（LUIS）门户中，迁移所有拥有的应用以使用 Azure 创作资源。

## <a name="prerequisites"></a>先决条件

* **（可选**）通过导出每个应用或使用导出[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)，从 LUIS 门户的 "应用" 列表备份应用。
* **还可以选择**保存每个应用的 collaborator's 列表。 此电子邮件列表作为迁移过程的一部分提供。
* **需要，需要**有一个[Azure 订阅](https://azure.microsoft.com/free/)。 订阅过程的一部分需要计费信息。 但是，在使用 LUIS 时，可以使用免费（F0）定价层。 最终可能会发现需要付费层，因为使用会增加。 

如果你没有 Azure 订阅，请[注册](https://azure.microsoft.com/free/)。 

## <a name="access-the-migration-process"></a>访问迁移过程

每周一次，系统都会提示您迁移应用程序。 无需迁移即可取消此窗口。 如果要在下一个计划的时间段之前迁移，可以从 LUIS 门户顶部工具栏上的 "**锁定**" 图标开始迁移过程。 

## <a name="app-owner-begins-the-migration-process"></a>应用所有者开始迁移过程

如果你是任何 LUIS 应用的所有者，则可以使用迁移过程。 

1. 1. 登录到[LUIS 门户](https://www.luis.ai)，并同意使用条款。
1. 可以在 "迁移" 弹出窗口中继续进行迁移或迁移。 选择 "**立即迁移**"。 如果选择稍后迁移，则在 Azure 中迁移到新的创作密钥需要9个月的时间。

    ![迁移过程中的第一个弹出窗口，选择 "立即迁移"。](./media/migrate-authoring-key/migrate-now.png)

1. 如果任何应用有协作者，系统将提示你向**他们发送一封电子邮件**，告知他们有关迁移的信息。 这是可选步骤。 将打开默认电子邮件应用程序，其中包含一种格式较轻的电子邮件。 

    ![发送有关迁移的协作者电子邮件](./media/migrate-authoring-key/send-collaborators-email.png)

1. 选择 "开始"，选择 "开始"，通过**创建要将应用迁移到的创作资源**来创建 LUIS 创作资源。 

    ![创建创作资源](./media/migrate-authoring-key/choose-authoring-resource.png)

1. 在下一个窗口中，输入资源键信息。 输入信息后，选择 "**创建资源**"。 

    ![创建创作资源](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    **创建新的创作资源**时，请提供以下信息： 

    * **资源名称**-所选的自定义名称，用作创作和预测终结点查询的 URL 的一部分。
    * **租户**-Azure 订阅与之关联的租户。 
    * **订阅名称**-将对资源计费的订阅。
    * **资源组**-选择或创建的自定义资源组名称。 资源组可让你将 Azure 资源分组以便进行访问和管理。 
    * **位置**-位置选项基于**资源组**选择。
    * **定价层**-定价层确定每秒和每月的最大事务数。 

1. 创建创作资源后，会显示成功消息。 选择 "**关闭**" 以关闭弹出窗口。

    ![创作资源已成功创建。](./media/migrate-authoring-key/migration-success.png)

    "**我的应用**" 列表显示迁移到新的创作资源的应用。 

    不需要知道创作资源的密钥即可继续在 LUIS 门户中编辑应用。 如果计划以编程方式编辑应用，则需要创作密钥值。 这些值显示在 LUIS 门户中的 "**管理-> Azure 资源**" 页上，也可以在资源的 "**密钥**" 页上的 "Azure 门户中找到。  

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>迁移过程完成后，将参与者添加到创作资源

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

了解[如何添加参与者](luis-how-to-collaborate.md)。 

## <a name="next-steps"></a>后续步骤


* 查看有关创作和运行时密钥的[概念](luis-concept-keys.md)
* 查看[如何分配密钥](luis-how-to-azure-subscription.md)并添加[参与者](luis-how-to-collaborate.md)
