---
title: ML Studio (经典) ：创建工作区-Azure
description: 若要使用 Azure 机器学习工作室（经典版），你必须拥有一个机器学习工作室（经典）工作区。 此工作区包含创建、管理和发布试验所需的工具。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 700cc461563f145f58e02f7ed9a09b2899a4eb5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91341442"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>创建并共享机器学习工作室（经典）工作区

**适用于：**  ![yes](../../../includes/media/aml-applies-to-skus/yes.png)机器学习工作室（经典）   ![no](../../../includes/media/aml-applies-to-skus/no.png)[Azure 机器学习](../compare-azure-ml-to-studio-classic.md)

若要使用 Azure 机器学习工作室（经典版），你必须拥有一个机器学习工作室（经典）工作区。 此工作区包含创建、管理和发布试验所需的工具。

## <a name="create-a-studio-classic-workspace"></a>创建工作室（经典）工作区

若要在机器学习工作室（经典版）中打开工作区，则必须登录到用于创建工作区的 Microsoft 帐户，或者需要收到所有者发出的邀请以加入工作区。 在 Azure 门户中可以管理工作区，包括配置访问权限。

1. 登录到 [Azure 门户](https://portal.azure.com/)

    > [!NOTE]
    > 只有 Azure 订阅管理员才能登录并创建工作室（经典）工作区。 
    >
    > 

2. 单击“+新建”

3. 在搜索框中，键入“机器学习工作室（经典）工作区”，并选择匹配项。 然后在页面底部选择单击“创建”。

4. 输入工作区信息：

   - *工作区名称*最多可以为 260 个字符，不能以空格结束。 该名称不能包含下列字符：`< > * % & : \ ? + /`
   - 如果从此工作区部署 Web 服务，将使用所选择（或创建）的 *Web 服务计划*以及所选的关联*定价层*。

     ![创建新的工作室（经典）工作区](./media/create-workspace/create-new-workspace.png)

5. 单击**创建**。

   机器学习当前只允许有限数量的区域。 如果订阅不包括这些区域之一，可能会看到错误消息，“你在允许的区域中没有订阅。”  要请求将某区域添加到订阅，可从 Azure 门户创建新的 Microsoft 支持请求，将“计费”选为问题类型，然后按照提示提交请求。


> [!NOTE]
> 机器学习工作室（经典）执行工作流时，依赖你提供的 Azure 存储帐户来保存中间数据。 创建工作区后，如果删除了存储帐户或如果更改了访问密钥，工作区将停止运行，该工作区中的所有试验会失败。
如果意外删除了存储帐户，可在相同区域重新创建与删除的存储帐户同名的存储帐户，并重新同步访问密钥。 如果更改了存储帐户访问密钥，请使用 Azure 门户在工作区中重新同步访问密钥。

部署工作区后，可以在机器学习工作室（经典）中打开它。

1. 浏览到机器学习工作室（经典），地址：[https://studio.azureml.net/](https://studio.azureml.net/)。

2. 在右上角选择工作区。

    ![选择工作区](./media/create-workspace/open-workspace.png)

3. 单击“我的试验”。

    ![打开试验](./media/create-workspace/my-experiments.png)

有关管理工作室（经典）工作区的信息，请参阅[管理 Azure 机器学习工作室（经典）工作区](manage-workspace.md)。
如果在创建工作区时遇到问题，请参阅[疑难解答指南：创建并连接到机器学习工作室（经典）工作区](index.yml)。


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>共享 Azure 机器学习工作室（经典）工作区
创建机器学习工作室（经典）工作区后，可以邀请用户进入工作区，以共享对工作区及其所有试验、数据集等的访问权限。可以将用户添加到以下两个角色之一：

* **用户** - 工作区用户可以在工作区中创建、打开、修改和删除试验、数据集等。
* **所有者** - 除了指定用户可以执行的操作之外，所有者还可以邀请和删除工作区中的用户。

> [!NOTE]
> 创建工作区的管理员帐户会自动添加到工作区，作为工作区所有者。 但是，不会向该订阅中的其他管理员或用户自动授予访问工作区的权限 - 需要显式邀请他们。
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>共享工作室（经典）工作区

1. 在 [https://studio.azureml.net/Home](https://studio.azureml.net/Home) 登录机器学习工作室（经典）

2. 在左面板中，单击“设置”

3. 单击“用户”选项卡

4. 单击页面底部的“邀请更多用户”

    ![工作室设置](./media/create-workspace/settings.png)

5. 输入一个或多个电子邮件地址。 用户需要有效的 Microsoft 帐户或组织帐户（来自 Azure Active Directory）。

6. 选择是要将用户添加为“所有者”还是“用户”。

7. 单击“确定”复选标记按钮。

添加的每个用户都将收到一封电子邮件，其中包含如何登录共享工作区的说明。

> [!NOTE]
> 要使用户能够在此工作区中部署或管理 Web 服务，这些用户必须是 Azure 订阅中的参与者或管理员。 

## <a name="troubleshoot-storage-accounts"></a>排查存储帐户问题


机器学习服务需要存储帐户以存储数据。 可以使用现有存储帐户，或者可以在创建新的机器学习工作室（经典版）工作区时创建新的存储帐户（如果有创建新的存储帐户的配额）。

创建新的机器学习工作室（经典版）工作区之后，可以使用用于创建工作区的 Microsoft 帐户登录到机器学习工作室（经典版）。 如果遇到错误消息，“未找到工作区”（类似于下面的屏幕截图），请使用以下步骤删除浏览器 cookie。

![未找到工作区](media/troubleshooting-creating-ml-workspace/screen3.png)

**删除浏览器 cookie**

1. 如果使用 Internet Explorer，请单击右上角的“工具”按钮，并选择“Internet 选项”。  

   ![Internet 选项](media/troubleshooting-creating-ml-workspace/screen4.png)

2. 在 "**常规**" 选项卡下，单击 "**删除 ...** "

   ![“常规”选项卡](media/troubleshooting-creating-ml-workspace/screen5.png)

3. 在“删除浏览历史记录”对话框中，确保已选中“Cookie 和网站数据”，并单击“删除”。

   ![删除 Cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

删除 Cookie 之后，重启浏览器，并转到 [Microsoft Azure 机器学习工作室（经典版）](https://studio.azureml.net)页。 如果系统提示输入用户名和密码，请输入用于创建工作区的同一个 Microsoft 帐户。


## <a name="next-steps"></a>后续步骤

有关管理工作区的详细信息，请参阅[管理 Azure 机器学习工作室（经典版）工作区](manage-workspace.md)。
