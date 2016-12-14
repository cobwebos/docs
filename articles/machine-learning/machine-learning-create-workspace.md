---
title: "创建机器学习工作区 | Microsoft Docs"
description: "如何为 Azure 机器学习工作室创建工作区"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: garye;bradsev;ahgyger
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 647398c1a0994da04845672e89d9f82d5e8b6d21


---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>创建并共享 Azure 机器学习工作区
此菜单链接到介绍如何设置用于 Cortana Analytics 过程 (CAPS) 的各种数据科学环境的主题。

[!INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

若要使用 Azure 机器学习工作室，需要具有机器学习工作区。 此工作空间包含创建、管理和发布试验所需的工具。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="to-create-a-workspace"></a>创建工作区
1. 登录到 [ Microsoft Azure 经典门户]。

> [!NOTE]
> 若要登录，需要是 Azure 订阅管理员。 机器学习工作区的所有者不会授予访问 [ Microsoft Azure 经典门户]的权限。 有关详细信息，请参阅 [Azure 订阅管理员和工作区所有者的特权](#subscriptionvsworkspace)。
> 
> 

1. 在 Microsoft Azure 服务面板中，单击“机器学习”。
   
    ![机器学习服务][1]
2. 单击窗口底部的“+ 新建”。
3. 依次单击“数据服务”、“机器学习”和“快速创建”。
   
    ![新工作区的快速创建][3]
4. 输入工作区的“工作区名称”。
5. 指定 Azure“位置”，然后输入现有的 Azure“存储帐户”或选择“新建”创建新的存储帐户。
6. 单击“创建 ML 工作区”。

创建机器学习工作区后，将看到它在“机器学习”页面上列出。

## <a name="sharing-an-azure-machine-learning-workspace"></a>共享 Azure 机器学习工作区
创建机器学习工作区后，可以邀请用户进入工作区，并共享对工作区及其所有试验的访问权限。 支持两类角色的用户：

* **用户** - 工作区用户可以在工作区中创建、打开、修改和删除数据集、试验和 Web 服务。
* **所有者** - 除了用户可以执行的操作之外，所有者还可以邀请、删除和列出具有工作区访问权限的用户。 他/她也有权访问 Notebook。

### <a name="to-share-a-workspace"></a>共享工作区
1. 登录到 [Azure 机器学习工作室]
2. 在机器学习工作室面板中，单击“设置”
3. 单击“用户”
4. 单击“邀请多个用户”
   
    ![邀请多个用户][4]
5. 输入一个或多个电子邮件地址。 用户只需要有效的 Microsoft 帐户（例如 name@outlook.com) 或组织帐户（来自 Azure Active Directory）。
6. 单击勾选按钮。

添加的每个用户都将收到一封电子邮件，其中包含登录共享工作区的说明。

有关管理工作区的信息，请参阅[管理 Azure 机器学习工作区]。
如果在创建工作区时遇到问题，请参阅[疑难解答指南：创建并连接到机器学习工作区]。

## <a name="a-namesubscriptionvsworkspaceaprivileges-of-azure-subscription-administrator-and-of-workspace-owner"></a><a name="subscriptionvsworkspace"></a> Azure 订阅管理员和工作区所有者的特权
下表说明 Azure 订阅管理员和工作区所有者之间的区别。

| 操作 | Azure 订阅管理员 | 工作区所有者 |
| --- |:---:|:---:|
| 访问 [ Microsoft Azure 经典门户] |是 |否 |
| 创建新的工作区 |是 |否 |
| 创建工作区 |是 |否 |
| 将终结点添加到 Web 服务 |是 |否 |
| 将终结点从 Web 服务中删除 |是 |否 |
| 更改 Web 服务的并发 |是 |否 |
| 访问[Azure 机器学习工作室] |否 * |是 |

> [!NOTE]
> * Azure 订阅管理员将自动添加到他/她所创建的工作区，作为工作区所有者。 但是，只是作为 Azure 订阅管理员并不会授予他/她对该订阅下的任何工作区的访问权限。
> 
> 

<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[创建工作区]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw4.png
[4]: media/machine-learning-create-workspace/cw5.png


<!--Link references-->
[管理 Azure 机器学习工作区]: machine-learning-manage-workspace.md
[疑难解答指南：创建并连接到机器学习工作区]: machine-learning-troubleshooting-creating-ml-workspace.md
[Azure 机器学习工作室]: https://studio.azureml.net/  
[ Microsoft Azure 经典门户]: https://manage.windowsazure.com/



<!--HONumber=Nov16_HO3-->


