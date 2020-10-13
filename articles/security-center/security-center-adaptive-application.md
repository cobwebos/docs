---
title: Azure 安全中心的自适应应用程序控制
description: 本文档介绍如何在 Azure 安全中心使用自适应应用程序控制将在 Azure 计算机中运行的应用程序加入允许列表。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2020
ms.author: memildin
ms.openlocfilehash: c3f3aad08266337756038dfa6ac6ff0c9afe093c
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996820"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>使用自适应应用程序控制来减少计算机的攻击面

了解 Azure 安全中心自适应应用程序控制的优势，以及可如何使用此数据驱动的智能功能增强安全性。


## <a name="what-are-security-centers-adaptive-application-controls"></a>安全中心的自适应应用程序控制是什么？

自适应应用程序控制是一种自动化智能解决方案，用于为计算机定义包含已知安全应用程序的允许列表。 

通常，组织拥有定期运行相同流程的计算机集合。 安全中心使用机器学习来分析计算机上运行的应用程序，并创建已知安全软件列表。 允许列表基于特定 Azure 工作负载，你可以使用下面的说明进一步自定义建议。

启用并配置自适应应用程序控制后，如果有任何运行的应用程序不是你定义为安全的应用程序，你将收到安全警报。


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>自适应应用程序控制有哪些优势？

通过定义已知安全应用程序列表，并在执行任何其他内容时生成警报，可以实现多个强化目标：

- 识别潜在的恶意软件，甚至是反恶意软件解决方案可能遗漏的任何恶意软件
- 改进对规定仅使用许可软件的本地安全策略的遵从性
- 避免运行旧的或不受支持的应用程序
- 防止使用组织禁止的特定软件
- 加强对访问敏感数据的应用的监管



## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|需要[用于服务器的 Azure Defender](defender-for-servers-introduction.md)|
|支持的计算机：|![是](./media/icons/yes-icon.png) 运行 Windows 和 Linux 的 Azure 和非 Azure 计算机<br>![是](./media/icons/yes-icon.png) [Azure Arc](https://docs.microsoft.com/azure/azure-arc/) 计算机|
|所需角色和权限：|“安全读者”和“读者”角色可以查看组和已知安全应用程序列表 <br>“参与者”和“安全管理员”角色可以查看组和已知安全应用程序的列表 |
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>在一组计算机上启用应用程序控制

如果安全中心在你的订阅中确定了始终运行一组相似应用程序的计算机组，则系统将提示以下建议：**应在计算机中启用自适应应用程序控制以定义安全应用程序**。

选择建议，或打开自适应应用程序控制页面，查看建议的已知安全应用程序列表和计算机组。

1. 打开 Azure Defender 仪表板，并从 "高级保护" 区域中选择 " **自适应应用程序控件**"。

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="从 Azure 仪表板打开自适应应用程序控件" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    “自适应应用程序控制”页随即打开，你的 VM 会分组到以下多个选项卡中：

    - **已配置** - 已具有定义的应用程序允许列表的计算机组。 对于每个组，“已配置”选项卡会显示：
        - 组中的计算机数
        - 最近的警报

    - **推荐** - 始终运行相同应用程序且未配置允许列表的计算机组。 我们建议你为这些组启用自适应应用程序控制。
    
      > [!TIP]
      > 如果你看到一个带有前缀“REVIEWGROUP”的组名，则该组名包含具有部分一致的应用程序列表的计算机。 安全中心不显示模式，但建议你查看此组以了解是否可以按照[编辑组的自适应应用程序控制规则](#edit-a-groups-adaptive-application-controls-rule)中所述，手动定义一些自适应应用程序控制规则。
      >
      > 你还可以将计算机从该组移动到其他组，如[将计算机从一个组移动到另一个组](#move-a-machine-from-one-group-to-another)中所述。

    - **无推荐** - 没有已定义的应用程序允许列表且不支持此功能的计算机。 你的计算机出现在此选项卡中可能是因为以下原因：
      - 缺少 Log Analytics 代理
      - Log Analytics 代理未发送事件
      - 这是一台 Windows 计算机，具有通过 GPO 或本地安全策略启用的预先存在的 [AppLocker](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) 策略

      > [!TIP]
      > 安全中心至少需要两周的数据才能定义每个计算机组的唯一推荐。 最近创建的计算机，或属于仅最近使用 Azure Defender 启用的订阅的计算机将显示在 " **无建议** " 选项卡下。


1. 打开“推荐”选项卡。此时将显示带有推荐允许列表的计算机组。

   ![“推荐”选项卡](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. 选择组。 

1. 要配置新规则，请查看此“配置应用程序控制规则”页的各个部分和内容，这些内容对于特定计算机组是唯一的：

   ![配置新规则](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **选择计算机** - 默认情况下，将选择标识组中的所有计算机。 如果取消选择任何计算机，则会此规则中删除它们。
   
   1. **推荐应用程序** - 查看此组中计算机的常用应用程序列表，并建议允许其运行。
   
   1. **更多应用程序** - 查看此应用程序列表，这些应用程序在该组计算机上不常出现，或者已知可被攻击。 一个警告图标，表示攻击者可能会利用特定应用程序绕过应用程序允许列表。 建议仔细检查这些应用程序。

      > [!TIP]
      > 两个应用程序列表都包含将特定应用程序限制为某些用户的选项。 尽可能采用最小特权原则。
      > 
      > 应用程序由其发布者定义，如果应用程序没有发布者信息（未签名），则会为特定应用程序的完整路径创建路径规则。

   1. 要应用规则，请选择“审核”。 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>编辑组的自适应应用程序控制规则

由于组织中的已知更改，你可能决定编辑一组计算机的允许列表。 

编辑计算机组的规则：

1. 打开 Azure Defender 仪表板，并从 "高级保护" 区域中选择 " **自适应应用程序控件**"。

1. 从“已配置”选项卡中，选择包含要编辑的规则的组。

1. 查看“配置应用程序控制规则”页的各个部分，如[在一组计算机上启用自适应应用程序控制](#enable-application-controls-on-a-group-of-machines)中所述。

1. （可选）添加一个或多个自定义规则：

   1. 选择“添加规则”。

      ![添加自定义规则](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. 如果要定义已知的安全路径，请将“规则类型”更改为“路径”。 可以在路径中包含通配符。
   
      > [!TIP]
      > 在路径中使用通配符可能有用的一些方案：
      > 
      > * 在路径末尾使用通配符，可以添加该文件夹和子文件夹中的所有可执行文件。
      > * 在路径中间使用通配符，可以启用文件夹名称发生更改的已知可执行文件名称（例如，包含已知可执行文件的个人用户文件夹、自动生成的文件夹名称等）。
  
   1. 定义允许的用户和受保护的文件类型。

   1. 定义完规则后，选择“添加”。

1. 选择“保存”，应用所做的更改。


## <a name="review-and-edit-a-groups-settings"></a>查看和编辑组的设置

1. 若要查看组的详细信息和设置，请选择 "**组设置**"

    此窗格显示了可) 、OS 类型、位置和其他相关详细信息修改 (的组的名称。

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="从 Azure 仪表板打开自适应应用程序控件" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

    > [!IMPORTANT]
    > 文件类型保护模式设置中的 " **强制** " 选项在所有方案中都灰显。 此时无强制选项可用。 
    >
    > :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-modes.png" alt-text="从 Azure 仪表板打开自适应应用程序控件" **应用** 并 **保存**"。



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>响应“应更新自适应应用程序控制策略中的允许列表规则”建议

如果安全中心的机器学习识别出以前不允许的可能合法的行为，你将看到此建议。 该建议提供针对现有定义的新规则，用于减少误报警报数量。

修正问题：

1. 从建议页中，选择“应更新自适应应用程序控制策略中的允许列表规则”建议，查看新标识的、可能合法的行为组。

1. 选择包含要编辑的规则的组。

1. 查看“配置应用程序控制规则”页的各个部分，如[在一组计算机上启用自适应应用程序控制](#enable-application-controls-on-a-group-of-machines)中所述。

1. 选择“审核”，应用所做的更改。




## <a name="audit-alerts-and-violations"></a>审核警报和冲突

1. 打开 Azure Defender 仪表板，并从 "高级保护" 区域中选择 " **自适应应用程序控件**"。

1. 要查看最近发出了警报的计算机组，请查看“已配置”选项卡中列出的组。

1. 要进一步调查，请选择一个组。

   ![最近的警报](./media/security-center-adaptive-application/recent-alerts.png)

1. 要查看更多详细信息以及受影响的计算机列表，请选择一个警报。



## <a name="move-a-machine-from-one-group-to-another"></a>将计算机从一个组移动到另一个组

将计算机从一个组移动到另一个组时，适用于该计算机的应用程序控制策略会更改为移动到的组的设置。 也可将计算机从已配置的组移动到未配置的组，这样做会删除应用于该计算机的所有应用程序控制规则。

1. 打开 Azure Defender 仪表板，并从 "高级保护" 区域中选择 " **自适应应用程序控件**"。

1. 在“自适应应用程序控制”页中，从“已配置”选项卡中选择包含要移动的计算机的组 。

1. 打开“已配置的计算机”列表。

1. 通过行尾的三个点打开计算机菜单，然后选择“移动”。 “将计算机移动到其他组”窗格随即打开。

1. 选择目标组，然后选择“移动计算机”。

1. 选择“保存”，以保存更改。





## <a name="manage-application-controls-via-the-rest-api"></a>通过 REST API 管理应用程序控制 

要以编程方式管理自适应应用程序控制，请使用我们的 REST API。 

[此处](https://docs.microsoft.com/rest/api/securitycenter/adaptiveapplicationcontrols)提供了完整 API 文档。

REST API 提供的一些函数：

* **List** 可检索所有组建议，并为每个组提供带有对象的 JSON。

* **Get** 可检索带有完整建议数据（即机器列表、发布者/路径规则等）的 JSON。

* **Put** 可用于配置规则（使用 Get 检索到的 JSON 作为此请求的主体）。
 
   > [!IMPORTANT]
   > **Put** 函数需要的参数比 Get 命令返回的 JSON 所含参数少。
   >
   > 在 Put 请求中使用 JSON 之前，请删除以下属性：recommendationStatus、configurationStatus、issues、location 和 sourceSystem。




## <a name="next-steps"></a>后续步骤
本文档介绍了如何在 Azure 安全中心使用自适应应用程序控制来定义 Azure 和非 Azure 计算机中运行的应用程序允许列表。 要详细了解安全中心的一些其他云工作负载保护功能，请参阅：

* [了解实时 (JIT) VM 访问](just-in-time-explained.md)
* [保护你的 Azure Kubernetes 群集](defender-for-kubernetes-introduction.md)