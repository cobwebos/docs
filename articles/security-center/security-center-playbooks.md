---
title: Azure 安全中心安全攻略 | Microsoft Docs
description: 本文档介绍如何使用 Azure 安全中心安全攻略来自动响应安全事件。
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: yurid
ms.openlocfilehash: 1d6fee9cb46fcfec32c0ecdb91eec09a56063c4e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Azure 安全中心安全攻略（预览版）
本文档介绍如何使用 Azure 安全中心安全攻略来响应安全相关问题。

## <a name="what-is-security-playbook-in-security-center"></a>安全中心安全攻略是什么？
安全攻略是可以在安全中心执行的过程的集合，其执行前提是已从所选警报触发特定的攻略。 可以通过安全攻略自动完成和协调对安全中心检测到的特定安全警报的响应。 安全中心安全攻略基于 [Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)。这意味着，你可以使用逻辑应用模板中安全类别下提供的模板，可以根据需要修改它们，还可以创建新的攻略，只需使用 [Azure 逻辑应用工作流](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app)，以及将安全中心用作触发器即可。 

> [!NOTE]
> 攻略会用到 Azure 逻辑应用，因此需付费。 有关更多详细信息，请访问 [Azure 逻辑应用](https://azure.microsoft.com/pricing/details/logic-apps/)定价页。 

## <a name="how-to-create-a-security-playbook-from-security-center"></a>如何在安全中心创建安全攻略？
请按以下步骤在安全中心创建新的安全攻略：

1.  打开“安全中心”仪表板。
2.  在左窗格中的“自动化和业务流程”部分单击“攻略(预览版)”。

    ![逻辑应用](./media/security-center-playbooks/security-center-playbooks-fig17.png)
 
3. 在“安全中心 - 攻略(预览版)”页中，单击“添加”按钮。

    ![创建逻辑应用](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. 在“创建逻辑应用”页中，键入创建新逻辑应用所需的请求信息，然后单击“创建”按钮。 创建完以后，新的攻略会显示在列表中。 如果不显示，请单击“刷新”按钮。 显示以后，即可通过单击来编辑该攻略。

    ![创建逻辑应用](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. 此时会显示逻辑应用设计器。 单击“空白逻辑应用”创建新的 playbook。 也可在类别下选择“安全性”，使用其中的一个模板。
    
    ![逻辑应用设计器](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. 在“搜索所有连接器和触发器”字段中，键入“Azure 安全中心”，然后选择“触发对 Azure 安全中心警报的响应时”。

    ![触发器](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. 现在可以定义触发攻略时的响应。 可以添加操作、逻辑条件、switch case 条件或循环。

    ![逻辑应用设计器](./media/security-center-playbooks/security-center-playbooks-fig5.png)
     
## <a name="how-to-run-a-security-playbook-in-security-center"></a>如何运行安全中心安全攻略？

在需要进行协调、从其他服务获取更多信息或进行修正时，可以运行安全中心安全攻略。 若要访问攻略，请执行以下步骤：

1.  打开“安全中心”仪表板。
2.  在左窗格的“威胁检测”下单击“安全事件和警报”。

    ![警报](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  单击要调查的警报。
4.  在警报页面顶部，单击“运行攻略”按钮。

    ![运行攻略](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. 在“攻略”页面选择要运行的攻略，然后单击“运行”按钮。 若要在触发之前查看攻略，可以单击该攻略，然后设计器就会打开。

    ![攻略](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>历史记录

运行该攻略以后，也可访问以前执行的攻略以及相关步骤，这些步骤包含以前执行的攻略的详细状态信息。 历史记录按警报进行上下文化，也就是说，在此页中看到的攻略历史记录与触发了此攻略的警报相关联。 

![历史记录](./media/security-center-playbooks/security-center-playbooks-fig16.png)

若要查看更多有关特定攻略执行情况的详细信息，请单击该攻略，然后就会显示包含整个工作流的逻辑应用运行页。

![详细信息](./media/security-center-playbooks/security-center-playbooks-fig14.png)

在此工作流中，可以看到执行每个任务所花的时间，还可以展开每个任务来查看结果。 

### <a name="changing-an-existing-playbook"></a>更改现有的攻略

可以更改安全中心的现有攻略，添加操作或条件。 为此，只需在“攻略”选项卡中单击要更改的攻略的名称，然后逻辑应用设计器就会打开。

> [!NOTE]
> 若要详细了解如何使用 Azure 逻辑应用创建自己的攻略，请阅读[创建第一个逻辑应用工作流以自动化云应用与云服务之间的流程](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app#add-an-action-that-responds-to-your-trigger)。


## <a name="see-also"></a>另请参阅
本文档介绍了如何使用 Azure 安全中心的攻略。 若要了解更多有关 Azure 安全中心的详细信息，请参阅以下内容：

* [Managing and responding to security alerts in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)（管理和响应 Azure 安全中心的安全警报）。 了解如何管理警报并响应安全中心的安全事件。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md)（在 Azure 安全中心进行安全运行状况监视）。 了解如何监视 Azure 资源的运行状况。
* [了解 Azure 安全中心中的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同类型的安全警报。
* [Azure 安全中心故障排除指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何排查安全中心的常见问题。 
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。

