---
title: 如何获取对 Avere vFXT for Azure 的支持
description: 创建有关 Avere vFXT for Azure 的支持票证的说明
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d621511cbb6983f8ad57ea8305823039475f40d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409604"
---
# <a name="get-help-with-your-system"></a>获得有关系统的帮助

如果需要有关 Avere vFXT for Azure 的帮助，可以通过以下各种方式获得支持：

* **Avere vFXT 问题** - 使用 Azure 门户为 Avere vFXT 创建支持票证，[如下](#open-a-support-ticket-for-your-avere-vfxt)所述。
* **配额** - 如果遇到配额相关问题，可[请求增加配额 ](#request-a-quota-increase)
* **文档和示例** - 如果发现该文档或示例存在问题，请滚动到存在问题的页面底部，然后使用“反馈”部分搜索现有问题并提交新文档（如有需要）。  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>为 Avere vFXT 创建支持票证

如果在部署或使用 Avere vFXT 时遇到问题，请通过 Azure 门户请求帮助。  

按照以下步骤操作，确保已使用群集中的资源标记支持票证。 标记票证有助于我们将其路由到正确的支持资源。 

1. 从 [https://portal.azure.com](https://portal.azure.com)，选择“资源组”。

   ![圈出“资源组”的 Azure 门户左侧菜单的屏幕截图](media/avere-vfxt-ticket-rg.png)

1. 浏览到包含存在问题的 vFXT 群集的资源组，然后单击其中一个 Avere 虚拟机。

    ![圈出特定 VM 的 Azure 门户资源组“概述”面板的屏幕截图](media/avere-vfxt-ticket-vm.png)

1. 在 VM 页面中，向下滚动到左侧面板的底部，然后单击“新建支持请求”。

    ![上一屏幕截图中 VM 的 Azure 门户 VM 页面的屏幕截图。 左侧菜单已滚动到底部，并圈出了“新建支持请求”。](media/avere-vfxt-ticket-request.png)

1. 在支持请求的第 1 页上，单击“所有服务”，然后在“存储”下进行查看，以选择“Avere vFXT”。

    ![Azure 门户中新建支持请求屏幕（标头为“基本信息”，“服务”项周围标有圆圈）的屏幕截图。 已选择“所有服务”按钮，下拉菜单字段的值为“Avere vFXT”](media/avere-vfxt-ticket-service.png)

1. 在第 2 页上，选择与问题最匹配的问题类型和类别。 添加简短标题和说明，说明中需包含问题出现的时间。 

   ![标头为“问题”的新建支持请求屏幕（其中包含许多需要填写的字段）的屏幕截图](media/avere-vfxt-ticket-problem.png)

1. 在第 3 页上，填写联系人信息，然后单击“创建”。 确认信息和票证号码将发送到你的电子邮件地址，支持人员成员将与你取得联系。

## <a name="request-a-quota-increase"></a>请求增加配额

请参阅 [vFXT 群集的配额](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)，了解部署 Avere vFXT for Azure 所需的组件。 可以从 Azure 门户[请求增加配额](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。