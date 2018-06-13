---
title: Azure 安全中心教程 - 响应安全事件 | Microsoft Docs
description: Azure 安全中心教程 - 响应安全事件
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: yurid
ms.openlocfilehash: a69ecc4bdff8e65c3529afab0c11afccfdd894c7
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774040"
---
# <a name="tutorial-respond-to-security-incidents"></a>教程：响应安全事件
安全中心使用高级分析和威胁智能来持续分析混合云工作负荷，在存在恶意活动时发出警报。 另外，你可以将其他安全产品和服务中的警报集成到安全中心，并根据自己的指示器或智能源创建自定义警报。 生成警报后，需采取快速行动进行调查和修正。 在本教程中，将了解如何：

> [!div class="checklist"]
> * 会审安全警报
> * 通过深入调查确定安全事件的根本原因和范围
> * 搜索有助于调查的安全数据

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件
若要逐步执行本教程中介绍的功能，你必须位于安全中心的“标准”定价层。 可以尝试安全中心标准版，头 60 天免费。 快速入门教程“[将 Azure 订阅载入到安全中心标准版](security-center-get-started.md)”详细介绍了如何升级到标准版。

## <a name="triage-security-alerts"></a>会审安全警报
安全中心提供所有安全警报的统一视图。 将会根据严重性设置安全警报的级别，并尽可能将相关的警报合并到一个安全事件中。 会审警报和事件时，应做到：

- 消除不需其他操作的警报。例如，如果警报为误报，则不需其他操作
- 采取行动对已知攻击造成的危害进行补救，例如，阻止源自恶意 IP 地址的网络流量
- 确定需要进一步调查的警报


1. 在安全中心主菜单的“检测”下，选择“安全警报”：

  ![安全警报](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)  

2. 在警报列表中单击某个安全事件（警报的集合），详细了解该事件。 此时会打开“检测到的安全事件”。

  ![安全事件](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. 此屏幕顶部为安全事件说明，底部为此事件包含的警报的列表。 单击要进一步调查的警报可获取更多信息。

  ![安全事件](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

  警报类型可能多种多样。请阅读[了解 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)一文，详细了解警报类型以及可能的修正步骤。 对于可以安全消除的警报，可右键单击警报，然后选择“消除”选项：

  ![警报](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. 如果恶意活动的根本原因和范围未知，请转到下一步进行深入调查。

## <a name="investigate-an-alert-or-incident"></a>调查警报或事件
1. 在“安全警报”页上单击“启动调查”按钮（如果已启动，则此按钮的名称会变为“继续调查”）。

  ![调查](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

  调查图以图形方式表示与此安全警报或事件相关的实体。 单击图中的一个实体就会显示有关该实体的信息（其中包含新的实体），且图会展开。 图中所选实体的属性在页面右侧的窗格中突出显示。 每个选项卡上提供的信息因所选实体而异。 在调查过程中，请查看所有相关的信息，更好地了解攻击者的活动。

2. 如果需要更多证据，或者必须对调查过程中发现的实体进行深入调查，请转到下一步。

## <a name="search-data-for-investigation"></a>搜索调查数据

可以使用安全中心的搜索功能查找系统受损的更多证据，以及调查过程中涉及的实体的更多详细信息。

若要进行搜索，请打开“安全中心”仪表板，在左侧导航窗格中单击“搜索”，选择包含要搜索的实体的工作区，键入搜索查询，然后单击搜索按钮。

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
本教程介绍了响应安全事件时需使用的安全中心功能，例如：

> [!div class="checklist"]
> * 安全事件，其中聚合了某个资源的相关警报
> * 调查图，以图形方式表示与某个安全警报或事件相关的实体
> * 搜索功能，用于查找系统受损的更多证据

若要详细了解安全中心的调查功能，请参阅：

> [!div class="nextstepaction"]
> [调查事件和警报](security-center-investigation.md)
