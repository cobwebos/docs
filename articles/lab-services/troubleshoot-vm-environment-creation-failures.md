---
title: 排查 VM 和环境故障 Azure 开发测试实验室
description: 了解如何对 Azure 开发测试实验室中的虚拟机（VM）和环境创建故障进行故障排除。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 155a359608cf6d846578306545f5ce0b4003949c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166348"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Azure 开发测试实验室中的虚拟机（VM）和环境创建故障疑难解答
如果计算机名称无效或者你即将违反实验室策略，开发测试实验室会发出警告。 有时，你会看到 "实验室 VM" 或 "环境状态" 旁边的红色 `X` 通知你发生了问题。  本文提供了几个技巧，你可以使用这些技巧来查找基本问题，并希望避免将来出现此问题。

## <a name="portal-notifications"></a>门户通知
如果你使用的是 Azure 门户，则首先要查看 "**通知" 面板**。  通知面板，可通过单击主命令栏上 **钟形图标** ，告诉您是否实验室 VM 或环境创建是成功还是失败。  如果出现故障，你会看到与创建失败相关联的错误消息。 详细信息通常会给予进一步的信息来帮助你解决问题。 在下面的示例中，虚拟机创建失败，因为内核不足。 详细消息告诉你如何解决此问题并请求增加核心配额。

![Azure 门户通知](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM 处于损坏状态
如果在实验室中看到 VM 的状态为 "**已损坏**"，则可能已从 **"虚拟**机" 页中删除了基础 vm，用户可以从**该页面导航**到该页面（而不是从开发测试实验室页面）。 通过从实验室删除 VM 来清理开发测试实验室中的实验室。 然后，在实验室中重新创建 VM。 

![VM 处于损坏状态](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>活动日志
如果要在尝试创建 VM 或环境之后调查故障，请查看活动日志。 本部分介绍如何查找 Vm 和环境的日志。

## <a name="activity-logs-for-virtual-machines"></a>虚拟机的活动日志

1. 在实验室的 "主页" 页上，选择 "要启动**虚拟机**的 VM" 页。
2. 在 "**虚拟机**" 页上，在左侧菜单的 "**监视**" 部分中，选择 "**活动日志**" 以查看与 VM 关联的所有日志。
3. 在 "活动日志项目" 中，选择失败的操作。 通常，失败的操作 `Write Virtualmachines`调用。
4. 在右侧窗格中，切换到 "JSON" 选项卡。日志的 JSON 视图中会显示详细信息。

    ![VM 的活动日志](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. 查看 JSON 日志，直到找到 `statusMessage` 属性。 它提供主要错误消息和详细信息（如果适用）。 以下 JSON 是本文前面提到的 "核心带引号已超过" 错误的示例。

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>环境的活动日志

若要查看环境创建的活动日志，请执行以下步骤：

1. 在实验室的主页上，选择左侧菜单中的 "**配置和策略**"。
2. 在 "**配置和策略**" 页上，选择菜单上的 "**活动日志**"。
3. 在日志的活动列表中查找失败，然后选择它。
4. 在右侧窗格中，切换到 "JSON" 选项卡，然后查找**statusMessage**。

    ![环境活动日志](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>资源管理器模板部署日志
如果你的环境或虚拟机是通过自动化创建的，则可以在最后一个位置查看错误信息。 这就是 Azure 资源管理器模板部署日志。 通过自动化创建实验室资源时，通常通过 Azure 资源管理器模板部署完成此操作。 请参阅创建开发测试实验室资源的示例 Azure 资源管理器模板[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) 。

若要查看实验室模板部署日志，请执行以下步骤：

1. 启动实验室所属资源组的页面。
2. 在 "**设置**" 下的左侧菜单中选择 "**部署**"。
3. 查找状态为 "失败" 的部署并选择它。
4. 在 "**部署**" 页上，选择失败操作的 "**操作详细信息**" 链接。
5. 你将在 "**操作详细信息**" 窗口中查看有关失败的操作的详细信息。

## <a name="next-steps"></a>后续步骤
请参阅[项目失败疑难解答](devtest-lab-troubleshoot-artifact-failure.md)
