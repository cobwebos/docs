---
title: 如何管理设置？ - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 了解如何在自定义翻译中管理设置、创建工作区、共享工作区和管理订阅密钥。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 6e8406cdd265063622bc5dcebbb9f22549b3633a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60513836"
---
# <a name="how-to-manage-settings"></a>如何管理设置

在自定义翻译的设置页中，可以创建新工作区、共享工作区，以及添加或修改 Microsoft Translation 订阅密钥。

访问设置页：

1. 登录到[自定义翻译](https://portal.customtranslator.azure.ai/)门户。
2. 在自定义翻译门户中，单击侧栏中的齿轮图标。

    ![“设置”链接](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>关联 Microsoft Translator 订阅

需要将一个 Microsoft 文本翻译 API 订阅密钥关联到工作区才能训练或部署模型。

如果没有订阅，请执行以下步骤：

1. 订阅 Microsoft 文本翻译 API。 本文将介绍如何订阅 Microsoft 文本翻译 API。
2. 记下翻译程序订阅的密钥。 密钥 1 或密钥 2 均可接受。
3. 导航回到自定义翻译门户。

### <a name="add-existing-key"></a>添加现有密钥

1.  导航到工作区的“设置”页。
2.  单击“添加密钥”

    ![如何添加订阅密钥](media/how-to/how-to-add-subscription-key.png)

3. 在对话框中，输入翻译程序订阅的密钥，然后单击“添加”按钮。

    ![如何添加订阅密钥](media/how-to/how-to-add-subscription-key-dialog.png)
4.  添加密钥后，随时可以修改或删除该密钥。

    ![添加后的订阅密钥](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>管理工作区

工作区是用于编写和构建自定义翻译系统的工作区域。 工作区可以包含多个项目、模型和文档。

如果需要将你的不同工作内容与不同的人员共享，创建多个工作区可能会有所帮助。

## <a name="create-a-new-workspace"></a>创建新的工作区

1.  导航到工作区的“设置”页。
2.  在“创建新工作区”部分单击“新建工作区”按钮。

    ![创建新工作区](media/how-to/create-new-workspace.png)

4.  在对话框中，输入新工作区的名称。
5.  单击“创建”。

    ![“创建新工作区”对话框](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>共享工作区

在自定义翻译中，如果需要将你的不同工作内容与不同的人员共享，可与其他人共享你的工作区。

1.  导航到工作区的“设置”页。
2.  在“共享设置”部分单击“共享”按钮。

    ![共享工作区](media/how-to/share-workspace.png)

3.  在对话框中，输入要与其共享此工作区的电子邮件地址的逗号分隔列表。 请确保与相应人员登录到自定义翻译时所用的电子邮件地址共享。 然后，选择适当的共享权限级别。

4.  如果工作区仍采用默认名称“我的工作区”，则共享该工作区之前必须更改名称。
5.  单击“保存”。

## <a name="sharing-permissions"></a>共享权限

1.  **读者：** 工作区中的读者可以查看工作区中的所有信息。

2.  **编辑者：** 工作区中的编辑者可以添加文档、训练模型，以及删除文档和项目。 他们可以添加订阅密钥，但无法修改工作区的共享对象、删除工作区，或更改工作区名称。

3.  **所有者：** 所有者对工作区拥有完全权限。

## <a name="change-sharing-permission"></a>更改共享权限

共享工作区后，“共享设置”部分将显示共享此工作区的所有电子邮件地址。 如果你对工作区拥有所有者访问权限，则可以更改每个电子邮件地址的现有共享权限。

1.  在每个电子邮件地址的“共享设置”部分，某个下拉菜单会显示当前权限级别。

2.  单击该下拉菜单，然后选择要分配给该电子邮件地址的新权限级别。

    ![共享权限设置](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>后续步骤

- 了解如何从 [Microsoft Translator Hub](https://hub.microsofttranslator.com) [迁移工作区和项目](how-to-migrate.md)
