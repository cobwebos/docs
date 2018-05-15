---
title: 在 Azure Blockchain Workbench 中使用应用程序
description: 如何在 Azure Blockchain Workbench 中使用应用程序合同。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b6de4f1df56d1ec80ed74c98f4e3a1db9d206612
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>在 Azure Blockchain Workbench 中使用应用程序

可以使用 Blockchain Workbench 来创建合同并对其执行操作。 也可查看合同详细信息，例如状态和事务历史记录。

## <a name="prerequisites"></a>先决条件

* Blockchain Workbench 部署。 有关部署的详细信息，请参阅 [Azure Blockchain Workbench 部署](blockchain-workbench-deploy.md)
* Blockchain Workbench 中部署的区块链应用程序。 请参阅 [在 Azure Blockchain Workbench 中创建区块链应用程序]()

在浏览器中[打开 Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url)。

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

需以 Blockchain Workbench 成员身份登录。 如果没有应用程序列出，则表明你是 Blockchain Workbench 的成员，但不是任何应用程序的成员。 Blockchain Workbench 管理员可以向应用程序分配成员。

## <a name="create-new-contract"></a>创建新合同 

若要创建新合同，你需要是 **AllowedInstanceRoles** 角色的成员。 

1. 在 Blockchain Workbench 应用程序部分，选择包含要创建的合同的应用程序磁贴。 此时会显示有效合同的列表。

2. 若要创建新的合同，请选择“新建合同”。

    ![“新建合同”按钮](media/blockchain-workbench-use/contract-list.png)

3. 此时会显示“新建合同”窗格。 指定初始参数值。 选择**创建**。

    ![“新建合同”窗格](media/blockchain-workbench-use/new-contract.png)

    新创建的合同与其他有效合同一起显示在列表中。

    ![有效合同列表](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>对合同执行操作

1. 在 Blockchain Workbench 应用程序部分，选择包含要采取操作的合同的应用程序磁贴。

    ![应用程序列表](media/blockchain-workbench-use/apps-list.png)

2. 在列表中选择合同。

    ![合同列表](media/blockchain-workbench-use/select-contract.png)

    有关合同的详细信息显示在不同部分。 

    ![合同详细信息](media/blockchain-workbench-use/contract-details.png)

    | 部分  | 说明  |
    |---------|---------|
    | 状态 | 列出合同各阶段的当前进度 |
    | 详细信息 | 合同的当前值 |
    | 操作 | 有关上一操作的详细信息 |
    | 活动 | 合同的事务历史记录 |
    
3. 在“操作”部分，选择“执行操作”。

4. 有关合同当前状态的详细信息显示在窗格中。 在下拉列表中选择要执行的操作。 

    ![执行操作](media/blockchain-workbench-use/take-action.png)

5. 选择“执行”以执行操作。

## <a name="next-steps"></a>后续步骤

[如何排查 Azure Blockchain Workbench 问题](blockchain-workbench-troubleshooting.md)