---
title: 创建机器学习工作室工作区 | Microsoft Docs
description: 如何为 Azure 机器学习工作室创建工作区
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 94502cbb0946ad1568cf33716480406b17fd57ac
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833948"
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>创建并共享 Azure 机器学习工作区
此菜单链接到介绍如何设置用于 Cortana Analytics 过程 (CAPS) 的各种数据科学环境的主题。

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

若要使用 Azure 机器学习工作室，需要具有机器学习工作区。 此工作空间包含创建、管理和发布试验所需的工具。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>创建工作区
1. 登录到 [Azure 门户](https://portal.azure.com/)

    > [!NOTE]
    > 若要登录并创建工作区，需要是 Azure 订阅管理员。 
    >
    > 

2. 单击“+新建”

3. 在搜索框中，键入“机器学习工作室工作区”，并选择匹配项。 然后在页面底部选择单击“创建”。

4. 输入工作区信息：

    - *工作区名称*最多可以为 260 个字符，不能以空格结束。 该名称不能包含下列字符：`< > * % & : \ ? + /`
    - 如果从此工作区部署 Web 服务，将使用所选择（或创建）的 *Web 服务计划*以及所选的关联*定价层*。

    ![创建新的工作区](./media/create-workspace/create-new-workspace.png)

5. 单击“创建”。

部署工作区后，可以在机器学习工作室中打开它。

1. 在 [https://studio.azureml.net/](https://studio.azureml.net/) 处浏览到机器学习工作室。

2. 在右上角选择工作区。

    ![选择工作区](./media/create-workspace/open-workspace.png)

3. 单击“我的试验”。

    ![打开试验](./media/create-workspace/my-experiments.png)

有关管理工作区的信息，请参阅[管理 Azure 机器学习工作区](manage-workspace.md)。
如果在创建工作区时遇到问题，请参阅[故障排除指南：创建并连接到机器学习工作区](troubleshooting-creating-ml-workspace.md)。


## <a name="sharing-an-azure-machine-learning-workspace"></a>共享 Azure 机器学习工作区
创建机器学习工作区后，可以邀请用户进入工作区，以共享对工作区及其所有试验、数据集、笔记本等的访问权限。可以将用户添加到以下两个角色之一：

* **用户** - 工作区用户可以在工作区中创建、打开、修改和删除试验、数据集等。
* **所有者** - 除了指定用户可以执行的操作之外，所有者还可以邀请和删除工作区中的用户。

> [!NOTE]
> 创建工作区的管理员帐户会自动添加到工作区，作为工作区所有者。 但是，不会向该订阅中的其他管理员或用户自动授予访问工作区的权限 - 需要显式邀请他们。
> 
> 

### <a name="to-share-a-workspace"></a>共享工作区

1. 在 [https://studio.azureml.net/Home](https://studio.azureml.net/Home) 处登录到机器学习工作室

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



