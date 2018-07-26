---
title: 在 Azure 开发测试实验室中向实验室发布通告 | Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中向实验室添加通告
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: ecfaf24d1122b711a93e1335b79acbbc4235bdae
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049943"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中向实验室发布通告

实验室管理员可以在现有实验室中发布自定义通告，以通知用户有关实验室最近发生的更改或新增内容。 例如，可将以下信息告知用户：

- 有新的 VM 大小可用
- 映像当前不可用
- 实验室策略已更新

发布后，通告将显示在实验室的“概述”页上，用户可以选择它来了解更多详细信息。

通告功能旨在用作临时通知。  不再需要时，可以轻松禁用通告。

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>在现有实验室中发布通告的步骤

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 如果需要，请选择“所有服务”，并从列表中选择“开发测试实验室”。 （实验室可能已显示在“所有资源”下的“仪表板”上）。
1. 从实验室列表中选择要在其中发布通告的实验室。  
1. 在实验室的“概览”区域中，选择“配置和策略”。  

    ![“配置和策略”按钮](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. 在左侧的“设置”下面，选择“实验室通告”。

    ![实验室通告按钮](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. 若要为此实验室中的用户创建消息，请将“已启用”设置为“是”。

1. 可以输入**到期日期**以指定向用户显示公告的截止日期和时间。 如果未输入到期日期，该公告将一直显示，直到禁用它。

   > [!NOTE]
   > 公告到期后，将不再向用户显示，但仍存在于“实验室公告”窗格中。 可以对其进行编辑，然后重新启用它使其重新处于活动状态。
   >
   >

1. 输入“通告标题”和“通告文本”。

   标题最多可包含 100 个字符，在实验室的“概述”页上向用户显示。 当用户选择该标题时，会显示通告文本。

   通告文本接受 Markdown。 输入通告文本时，可以在屏幕底部的“预览”区域中查看消息。

    ![用于创建消息的实验室通告屏幕。](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. 准备好发布通告后，请选择“保存”。

如果不再想要向实验室用户显示此通告，请返回“实验室通告”页，并将“已启用”设置为“否”。 如果指定了到期日期，公告将在该日期和时间自动被禁用。

## <a name="steps-for-users-to-view-an-announcement"></a>用户查看通告的步骤

1. 在 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)中，选择一个实验室。

1. 如果已针对该实验室发布通告，则该实验室的“概述”页顶部会显示信息通知。 此信息通知是创建通告时指定的通告标题。

    ![“概述”页上的实验室通告](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. 用户可以选择消息来查看整个通告。

    ![有关实验室通告的详细信息](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Azure 资源管理器模板
可以将公告指定为 Azure 资源管理器模板的一部分，如以下示例所示： 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }                
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

可以使用以下方法之一部署 Azure 资源管理器模板：

- [Azure 门户](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

## <a name="next-steps"></a>后续步骤
* 如果更改或设置了实验室策略，可以发布通告来告知用户。 [设置策略和计划](devtest-lab-set-lab-policy.md)提供了有关使用自定义策略对整个订阅应用限制和约定的信息。
* 浏览[开发测试实验室 Azure 资源管理器快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/Samples)。
