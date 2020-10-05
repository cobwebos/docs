---
title: include 文件
description: include 文件
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.openlocfilehash: fda9df6c7e9651bbd3b0b70ad9d47f23c0c19d01
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91541419"
---
## <a name="sign-in-to-luis-portal"></a>登录到 LUIS 门户

LUIS 的新用户需要执行此过程：

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择你的国家/地区，并同意使用条款。 如果看到“我的应用”，则 LUIS 资源已存在，你应该跳过此过程，开始创建应用。 有关受支持的区域，请访问[创作和发布区域及关联的密钥](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions)。

1. 选择“创建 Azure资源” ，然后选择“创建要将应用迁移到的创作资源”。

    ![选择语言理解创作资源的类型](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 填写资源的详细信息。

    ![屏幕截图显示了“新建用于创作的资源”窗格。](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    **创建新的创作资源**时，请提供以下信息：

    * **资源名称** - 你选择的自定义名称，用作创作和预测终结点查询的 URL 的一部分。
    * **租户** - 与 Azure 订阅关联的租户。
    * **订阅名称** - 将对资源计费的订阅。
    * **资源组** - 你选择或创建的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。
    * **位置** - 位置选项基于**资源组**选择。
    * **定价层** - 定价层确定每秒和每月的最大事务数。

1. 此时将显示要创建的资源的摘要。 选择“**下一页**”。

    ![屏幕截图显示了“欢迎”页面，其中显示了用于链接到 Azure 帐户的选项。](../media/sign-in/sign-in-confirm-key-selection.png)

1. 选择“继续”进行确认。

    ![屏幕截图显示了链接到 Azure 帐户后的“欢迎”页面。](../media/sign-in/sign-in-confirm-continue.png)
