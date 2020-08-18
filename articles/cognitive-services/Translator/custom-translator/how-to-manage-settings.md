---
title: 如何管理设置？ - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 了解如何在自定义翻译中管理设置、创建工作区、共享工作区和管理订阅密钥。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 488ca52abdd5f8d6cfd1f92a5f91861a17a77d19
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510442"
---
# <a name="how-to-manage-settings"></a>如何管理设置

在 "自定义转换器设置" 页中，可以共享工作区、修改翻译人员订阅密钥和删除工作区。

访问设置页：

1. 登录到[自定义翻译](https://portal.customtranslator.azure.ai/)门户。
2. 在自定义翻译门户中，单击侧栏中的齿轮图标。

    ![“设置”链接](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>关联转换器订阅

需要有与工作区关联的转换器订阅密钥来定型或部署模型。

如果没有订阅，请执行以下步骤：

1. 订阅以创建转换器资源。 请按照 [如何注册转换器](https://docs.microsoft.com/azure/cognitive-services/translator/translator-how-to-signup) 以订阅和获取 translator 密钥进行注册。
2. 记下你的翻译人员订阅的密钥。 密钥 1 或密钥 2 均可接受。
3. 导航回到自定义翻译门户。

## <a name="create-a-new-workspace"></a>创建新的工作区

1. 单击 "自定义转换器" 侧栏中的 "+ 创建工作区" 按钮。

    ![创建新工作区](media/how-to/create-new-workspace.png)

2. 在对话框中，输入新工作区的名称。
3. 单击 "下一步"。
4. 选择 "订阅类型"。
5. 选择 "订阅区域"。 创建 Translator 资源密钥时，该区域必须与所选区域匹配。
6. 输入转换器订阅的密钥，然后单击 "保存" 按钮。

    ![“创建新工作区”对话框](media/how-to/create-new-workspace-dialog.png)


### <a name="modify-existing-key"></a>修改现有密钥

1. 导航到工作区的“设置”页。
2. 单击 "更改密钥"

    ![如何添加订阅密钥](media/how-to/how-to-add-subscription-key.png)

3. 在对话框中，输入转换器订阅的密钥，然后单击 "保存" 按钮。

    ![如何添加订阅密钥](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>管理工作区

工作区是用于编写和构建自定义翻译系统的工作区域。 工作区可以包含多个项目、模型和文档。

如果需要将你的不同工作内容与不同的人员共享，创建多个工作区可能会有所帮助。

## <a name="share-your-workspace"></a>共享工作区

在自定义翻译中，如果需要将你的不同工作内容与不同的人员共享，可与其他人共享你的工作区。

1. 导航到工作区的“设置”页。
2. 单击 "共享设置" 部分中的 "添加人员" 按钮。

    ![共享工作区](media/how-to/share-workspace.png)

3. 在对话框中，输入要与其共享此工作区的电子邮件地址的逗号分隔列表。 请确保与相应人员登录到自定义翻译时所用的电子邮件地址共享。 然后，选择适当的共享权限级别，然后单击 "保存" 按钮。

    ![共享工作区对话框](media/how-to/share-workspace-dialog.png)

4. 如果工作区仍采用默认名称“我的工作区”，则共享该工作区之前必须更改名称。
5. 单击“保存”。

## <a name="sharing-permissions"></a>共享权限

1. **读取者：** 工作区中的读取者可以查看工作区中的所有信息。

2. **编辑者：** 工作区中的编辑者可以添加文档、训练模型，以及删除文档和项目。 他们可以添加订阅密钥，但无法修改工作区的共享对象、删除工作区，或更改工作区名称。

3. **所有者：** 所有者对工作区拥有完全权限。

## <a name="change-sharing-permission"></a>更改共享权限

共享工作区时，"共享设置" 部分将显示与此工作区共享的所有电子邮件地址。 如果你对工作区拥有所有者访问权限，则可以更改每个电子邮件地址的现有共享权限。

1. 在每封电子邮件的 "共享设置" 部分中，下拉菜单显示当前的权限级别。

2. 单击该下拉菜单，然后选择要分配给该电子邮件地址的新权限级别。

    ![共享权限设置](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>固定工作区

默认情况下，第一个创建的工作区是固定的。 每次登录时，固定工作区都会显示在站点负载上。 如果你已创建多个工作区，并希望在登录时将其中一个工作区设为默认值，则需要将其固定。

1. 在侧栏中，单击要固定的工作区的名称。
2. 导航到工作区的 "设置" 页。
3. 单击 "固定" 图标。

    ![固定工作区](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>后续步骤

- 了解 [如何创建工作区和项目](workspace-and-project.md)
