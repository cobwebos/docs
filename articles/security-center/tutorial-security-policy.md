---
title: Azure 安全中心教程 - 定义和评估安全策略 | Microsoft Docs
description: Azure 安全中心教程 - 定义和评估安全策略
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: yurid
ms.openlocfilehash: 16dc8553fdc1209d1973934a87660ff61df8e68a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-define-and-assess-security-policies"></a>教程：定义和评估安全策略
可以通过安全中心使用安全策略来定义工作负荷的所需配置，确保用户遵守公司或法规方面的安全要求。 为 Azure 订阅定义策略并根据工作负荷类型或数据敏感性对其进行调整以后，即可通过安全中心获取计算、网络、SQL 和存储以及应用程序资源方面的安全建议。 在本教程中，将了解如何：

> [!div class="checklist"]
> * 配置安全策略
> * 评估资源的安全性

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>先决条件
若要逐步执行本教程中介绍的功能，你必须位于安全中心的“标准”定价层。 可以尝试安全中心标准版，头 60 天免费。 快速入门教程“[将 Azure 订阅载入到安全中心标准版](security-center-get-started.md)”详细介绍了如何升级到标准版。

## <a name="configure-security-policy"></a>配置安全策略
安全中心自动为每个 Azure 订阅创建默认的安全策略。 可以根据该订阅的安全要求，启用或关闭安全策略中包含的建议。 若要更改默认的安全策略，你必须是订阅的所有者、参与者或安全管理员。

1. 在“安全中心”主菜单上，选择“安全策略”。 选择要使用的订阅。 在“策略组件”下选择“安全策略”：

  ![安全策略](./media/tutorial-security-policy/tutorial-security-policy-fig1.png)  

2. 对于每个要监视的安全配置，请选择“启用”。 安全中心会持续评估环境的配置。如果存在漏洞，安全中心会生成安全建议。 如果不建议启用该安全配置，或者该安全配置不相关，请选择“关闭”。 例如，开发/测试环境中需要的安全级别可能不同于生产环境。 选择适用于环境的策略以后，请单击“保存”。

等待安全中心处理这些策略并生成建议。 某些配置（例如系统更新和 OS 配置）可能需要长达 12 小时的时间，而网络安全组和加密配置则几乎可以立即进行评估。 在“安全中心”仪表板中看到建议以后，即可进入下一步。

## <a name="assess-security-of-resources"></a>评估资源的安全性
1. 根据已启用的安全策略，安全中心会视需要提供一组安全建议。 一开始应查看虚拟机和计算机建议。 在“安全中心”仪表板中单击“概览”，然后单击“计算”。

  ![计算](./media/tutorial-security-policy/tutorial-security-policy-fig2.png)

  查看每项建议，以红色（优先级高）的占比确定建议的优先级。 这些建议中的一部分所提供的修正可以直接通过安全中心来实现，例如[终结点保护问题](https://docs.microsoft.com/azure/security-center/security-center-install-endpoint-protection)。 其他建议仅提供修正应用指南，例如“缺少磁盘加密”建议。

2. 解决所有相关的计算建议以后，应转到下一工作负荷：网络。 在“安全中心”仪表板中单击“概览”，然后单击“网络”。

  ![网络](./media/tutorial-security-policy/tutorial-security-policy-fig3.png)

  网络建议页有一个列表，其中包含网络配置、面向 Internet 的终结点和网络拓扑方面的问题。 就像“计算”一样，某些网络建议会提供集成的修正，而另一些则不会。

3. 解决所有相关的网络建议以后，应转到下一工作负荷：存储与数据。 在“安全中心”仪表板中单击“概览”，然后单击“存储与数据”。

  ![数据资源](./media/tutorial-security-policy/tutorial-security-policy-fig4.png)

  “数据资源”页包含的建议涉及启用 Azure SQL 服务器和数据库审核、启用 SQL 数据库加密，以及启用 Azure 存储帐户加密。 如果没有这些工作负荷，则不会看到任何建议。 就像“计算”一样，某些 SQL 与存储建议会提供集成的修正，而另一些则不会。

4. 解决所有相关的 SQL 与存储建议以后，应转到下一工作负荷：应用程序。 在“安全中心”仪表板中单击“概览”，然后单击“应用程序”。

  ![应用程序](./media/tutorial-security-policy/tutorial-security-policy-fig5.png)

  “应用程序”页包含 Web 应用程序防火墙部署的建议，以及应用程序强化的一般准则。 如果虚拟机或计算机没有在 Internet Information Service (IIS) 上运行的 Web 应用程序，则不会看到这些建议。

## <a name="clean-up-resources"></a>清理资源
本系列中的其他快速入门和教程是在本快速入门的基础上制作的。 如果打算继续学习后续的快速入门和教程，请继续运行“标准”层并让自动预配保持启用状态。 如果不打算继续或想要返回到“免费”层，请执行以下操作：

1. 返回到安全中心主菜单，选择“安全策略”。
2. 选择要返回到“免费”层的订阅或策略。 此时会打开“安全策略”。
3. 在“策略组件”下选择“定价层”。
4. 选择“免费”，将订阅从“标准”层更改为“免费”层。
5. 选择“保存”。

如果希望禁用自动预配，请执行以下操作：

1. 返回到安全中心主菜单，选择“安全策略”。
2. 选择希望禁用自动设置的订阅。
3. 在“安全策略 - 数据收集”下的“载入”下选择“关闭”，禁用自动预配。
4. 选择“保存”。

>[!NOTE]
> 禁用自动设置不会从已预配代理的 Azure VM 中删除 Microsoft Monitoring Agent。 禁用自动设置会限制对资源的安全监视。
>

## <a name="next-steps"></a>后续步骤
本教程介绍了如何通过安全中心对工作负荷进行基本的策略定义和安全评估，例如：

> [!div class="checklist"]
> * 安全策略配置，确保用户遵守公司或法规方面的安全要求
> * 对计算、网络、SQL 与存储以及应用程序资源进行的安全评估

若要了解如何通过安全中心来保护资源，请转到下一教程。

> [!div class="nextstepaction"]
> [保护资源](tutorial-protect-resources.md)
