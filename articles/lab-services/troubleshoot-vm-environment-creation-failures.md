---
title: 解决 VM 和环境故障 Azure 开发人员测试实验室
description: 了解如何在 Azure 开发人员测试实验室中排除虚拟机 （VM） 和环境创建故障。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166348"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>在 Azure 开发人员测试实验室中排除虚拟机 （VM） 和环境创建失败
如果计算机名称无效或即将违反实验室策略，DevTest Labs 会为您提供警告。 有时，在实验室`X`VM 或环境状态旁边看到红色，这些红色会通知您出现问题。  本文提供了一些技巧，您可以使用这些技巧来查找根本问题，并希望将来避免此问题。

## <a name="portal-notifications"></a>门户通知
如果使用 Azure 门户，则首先查看的是**通知面板**。  通过单击**铃形图标**，主命令栏上可用的通知面板将告诉您实验室 VM 或环境创建是否成功。  如果失败，您将看到与创建失败关联的错误消息。 详细信息通常会提供进一步的信息，以帮助您解决问题。 在下面的示例中，由于内核不足，虚拟机创建失败。 详细消息告诉您如何解决此问题并请求增加核心配额。

![Azure 门户通知](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM 处于损坏状态
如果您在实验室中看到 VM 的状态为 **"已损坏**"，则基础 VM 可能已从用户可以从**虚拟机**页面导航到的**虚拟机**页面中删除（而不是从 DevTest Labs 页面）。 通过从实验室中删除 VM，清理开发人员实验室中的实验室。 然后，在实验室中重新创建 VM。 

![处于损坏状态的 VM](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>活动日志
如果在尝试创建 VM 或环境后某个时候调查故障，请查看活动日志。 本节介绍如何查找 VM 和环境的日志。

## <a name="activity-logs-for-virtual-machines"></a>虚拟机的活动日志

1. 在实验室的主页上，选择 VM 以启动**虚拟机**页面。
2. 在 **"虚拟机**"页上，在左侧菜单的 **"监视"** 部分中，选择 **"活动日志"** 以查看与 VM 关联的所有日志。
3. 在活动日志项中，选择失败的操作。 通常，失败操作称为`Write Virtualmachines`。
4. 在右侧窗格中，切换到 JSON 选项卡。您可以在日志的 JSON 视图中看到详细信息。

    ![VM 的活动日志](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. 查看 JSON 日志，直到找到该`statusMessage`属性。 它为您提供主错误消息和进一步的详细信息（如果适用）。 以下 JSON 是本文前面看到的核心引用超过错误的示例。

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>环境的活动日志

要查看创建环境的活动日志，请按照以下步骤操作：

1. 在实验室的主页上，选择左侧菜单上的 **"配置"和"策略**"。
2. 在 **"配置和策略**"页上，选择菜单上**的活动日志**。
3. 在日志中的活动列表中查找失败并选择它。
4. 在右侧窗格中，切换到 JSON 选项卡，然后查找**状态Message**。

    ![环境活动日志](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>资源管理器模板部署日志
如果环境或虚拟机是通过自动化创建的，则有最后一个位置可以查看错误信息。 这是 Azure 资源管理器模板部署日志。 通过自动化创建实验室资源时，通常通过 Azure 资源管理器模板部署完成。 有关[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)创建 DevTest Labs 资源的示例 Azure 资源管理器模板，请参阅。

要查看实验室模板部署日志，请按照以下步骤操作：

1. 启动存在实验室的资源组的页面。
2. 在 **"设置"** 下选择左侧菜单上**的部署**。
3. 查找状态失败的部署并选择它。
4. 在 **"部署"** 页上，为失败的操作选择 **"操作详细信息**"链接。
5. 在 **"操作详细信息"** 窗口中可以看到有关失败操作的详细信息。

## <a name="next-steps"></a>后续步骤
请参阅[排除工件故障](devtest-lab-troubleshoot-artifact-failure.md)
