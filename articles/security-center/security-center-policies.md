---
title: "在 Azure 安全中心设置安全策略 | Microsoft Docs"
description: "本文档介绍了如何在 Azure 安全中心配置安全策略。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: 67564e930310433bf4d51f7642bdd7ebf7e8e600
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>在 Azure 安全中心设置安全策略
本文档介绍如何在安全中心配置安全策略，指导用户完成执行此任务所必需的步骤。


## <a name="how-security-policies-work"></a>安全策略工作原理
安全中心自动为每个 Azure 订阅创建默认的安全策略。 可以在安全中心编辑该策略，也可以使用 [Azure 策略](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)创建新的定义、定义其他策略、跨管理组（可以代表整个组织、组织中的某个业务部门，等等）分配策略，以及跨相应范围监视对这些策略的遵循情况。

> [!NOTE]
> Azure 策略为有限预览版。 单击[此处](https://aka.ms/getpolicy)即可加入。 有关 Azure 策略的详细信息，请阅读 [Create and manage policies to enforce compliance](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy)（创建和管理策略以强制要求符合性）。

## <a name="how-to-change-security-policies-in-security-center"></a>如何在安全中心更改安全策略？
可以在安全中心为每个 Azure 订阅编辑默认的安全策略。 若要修改安全策略，你必须是该订阅或包含型管理组的所有者或安全管理员。 登录到 Azure 门户，按照后续步骤在安全中心查看安全策略：

1. 在“安全中心”仪表板的“通用”下单击“安全策略”。
2. 选择要在其上启用安全策略的订阅。

    ![策略管理](./media/security-center-policies/security-center-policies-fig10.png)

3. 在“策略组件”部分单击“安全策略”。

    ![策略组件](./media/security-center-policies/security-center-policies-fig12.png)

4. 这是通过 Azure 策略分配给安全中心的默认策略。 可以删除“策略和参数”下的项，也可以在“可用选项”下添加其他策略定义。 为此，可直接单击定义名称旁的加号。

    ![策略定义](./media/security-center-policies/security-center-policies-fig11.png)

5. 如果需要有关策略的更详细说明，请单击该策略，此时会打开另一页面，其中包含详细信息以及具有 [策略定义(https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy#policy-definition-structure) 结构的 JSON 代码：

    ![Json](./media/security-center-policies/security-center-policies-fig13.png)

6. 编辑完后，单击“保存”。

## <a name="see-also"></a>另请参阅
在本文档中，已经学习了如何在 Azure 安全中心中配置安全策略。 若要详细了解 Azure 安全中心，请参阅以下内容：

* [Azure Security Center planning and operations guide](security-center-planning-and-operations-guide.md)（Azure 安全中心规划和操作指南）。 了解如何规划并理解设计注意事项，以便采用 Azure 安全中心。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md)（在 Azure 安全中心进行安全运行状况监视）。 了解如何监视 Azure 资源的运行状况。
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)（管理和响应 Azure 安全中心的安全警报）。 了解如何管理和响应安全警报。
* [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md)（通过 Azure 安全中心监视合作伙伴解决方案）。 了解如何监视合作伙伴解决方案的运行状况。
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。
