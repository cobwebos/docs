---
title: Azure 安全中心的自适应应用程序控制
description: 本文档可帮助你在 Azure 安全中心使用自适应应用程序控制，以允许列出在 Azure 计算机中运行的应用程序。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2020
ms.author: memildin
ms.openlocfilehash: 17a3d0364f3085e4baf4e0faa40f117d9fbbb04f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268156"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>使用自适应应用程序控件来减少计算机的攻击面

了解 Azure 安全中心的自适应应用程序控件的优点，以及如何利用此数据驱动的智能功能增强安全性。


## <a name="what-are-security-centers-adaptive-application-controls"></a>什么是安全中心的自适应应用程序控件？

自适应应用程序控件是一种智能和自动化的解决方案，用于定义计算机的已知安全应用程序的允许列表。 

通常，组织具有定期运行相同进程的计算机的集合。 安全中心使用机器学习来分析计算机上运行的应用程序，并创建一系列已知安全的软件。 允许列表基于特定的 Azure 工作负荷，你可以使用以下说明进一步自定义建议。

启用并配置自适应应用程序控件后，如果任何应用程序运行的不是您定义为安全的应用程序，您将收到安全警报。


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>自适应应用程序控件的好处是什么？

通过定义已知安全的应用程序列表，并在执行任何其他操作时生成警报，你可以实现多个强化目标：

- 识别潜在的恶意软件，甚至反恶意软件解决方案可能丢失的恶意软件
- 提高了本地安全策略的符合性，该策略规定仅使用许可的软件
- 避免运行旧应用程序或不受支持的应用程序
- 阻止组织禁止的特定软件
- 增加访问敏感数据的应用的监督



## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|需要 [用于服务器的 Azure Defender](defender-for-servers-introduction.md)|
|支持的计算机：|![是 ](./media/icons/yes-icon.png) azure 和运行 Windows 和 Linux 的非 azure 计算机<br>![是 ](./media/icons/yes-icon.png) [Azure Arc](https://docs.microsoft.com/azure/azure-arc/) 计算机|
|所需角色和权限：|**安全读者** 和 **读取** 者角色可以查看组和已知安全应用程序的列表<br>**参与者** 和 **安全管理员** 角色可以编辑组和已知安全应用程序的列表|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>在一组计算机上启用应用程序控件

如果安全中心在你的订阅中标识了一致运行一组类似的应用程序的计算机组，则系统会提示你提供以下建议： **自适应应用程序控件，以便在你的计算机上启用用于定义安全应用程序的自适应应用程序控件**。

选择建议，或打开 "自适应应用程序控制" 页，查看建议的已知安全应用程序和计算机组的列表。

1. 打开 Azure Defender 仪表板，并从 "高级保护" 区域中选择 " **自适应应用程序控件**"。

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="从 Azure 仪表板打开自适应应用程序控件" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    此时将打开 " **自适应应用程序控制** " 页，并将 vm 分组为以下选项卡：

    - 已**配置**-已定义允许应用程序列表的计算机组。 对于每个组，"已配置" 选项卡显示：
        - 组中的计算机数
        - 最近的警报

    - **建议** -运行相同应用程序且未配置允许列表的计算机组。 建议为这些组启用自适应应用程序控件。
    
      > [!TIP]
      > 如果你看到名为 "REVIEWGROUP" 的组名，则它包含具有部分一致性列表的应用程序的计算机。 安全中心无法查看模式，但建议查看此组，以查看 _是否可以_ 手动定义某些自适应应用程序控件规则，如 [编辑组的自适应应用程序控件规则](#edit-a-groups-adaptive-application-controls-rule)中所述。
      >
      > 你还可以根据 [将计算机从一个组移到另一个组](#move-a-machine-from-one-group-to-another)中所述，将计算机从此组移动到其他组。

    - **无建议** -没有定义的应用程序的允许列表和不支持此功能的计算机。 您的计算机可能在此选项卡中，原因如下：
      - 缺少 Log Analytics 代理
      - Log Analytics 代理未发送事件
      - 它是一个 Windows 计算机，其中包含由 GPO 或本地安全策略启用的预先存在的 [AppLocker](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) 策略

      > [!TIP]
      > 安全中心需要至少两周的数据来定义每组计算机的唯一建议。 最近创建的计算机，或属于仅最近使用 Azure Defender 启用的订阅的计算机将显示在 " **无建议** " 选项卡下。


1. 打开 **建议** 的选项卡。此时会显示具有 "推荐允许列表" 的计算机组。

   ![建议的选项卡](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. 选择组。 

1. 若要配置新规则，请查看此 " **配置应用程序控制规则** " 页和内容的各个部分，这些部分对于这一特定计算机组是唯一的：

   ![配置新规则](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **选择计算机** -默认情况下，已标识组中的所有计算机都处于选中状态。 取消选择任何以从该规则中删除它们。
   
   1. **推荐的应用程序** -查看此组中的计算机共用的此应用程序列表，并建议允许运行。
   
   1. **更多应用程序** -查看此组中的计算机上不太频繁显示的此应用程序列表，或查看这些应用程序的可攻击列表。 警告图标表示攻击者可以使用特定的应用程序绕过应用程序允许列表。 建议仔细查看这些应用程序。

      > [!TIP]
      > 这两个应用程序列表包括将特定应用程序限制为特定用户的选项。 尽可能采用最小特权原则。
      > 
      > 应用程序是由其发布者定义的。如果应用程序没有 (未签名) 的发行者信息，将为特定应用程序的完整路径创建路径规则。

   1. 若要应用规则，请选择 " **审核**"。 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>编辑组的自适应应用程序控件规则

由于组织中已知的更改，你可能会决定编辑一组计算机的允许列表。 

编辑计算机组的规则：

1. 打开 Azure Defender 仪表板，并从 "高级保护" 区域中选择 " **自适应应用程序控件**"。

1. 从 " **配置** " 选项卡中，选择包含要编辑的规则的组。

1. 如在[一组计算机上启用自适应应用程序控件](#enable-application-controls-on-a-group-of-machines)中所述，查看 "**配置应用程序控制规则**" 页的各个部分。

1. （可选）添加一个或多个自定义规则：

   1. 选择 " **添加规则**"。

      ![添加自定义规则](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. 如果要定义已知的安全路径，请将 **规则类型** 更改为 "path"。 可以在路径中包含通配符。
   
      > [!TIP]
      > 路径中的通配符可能有用的一些方案：
      > 
      > * 在路径末尾使用通配符可允许此文件夹和子文件夹中的所有可执行文件。
      > * 使用路径中间的通配符启用具有更改的文件夹名称的已知可执行文件名称 (例如，包含已知可执行文件、自动生成的文件夹名称等) 的个人用户文件夹。
  
   1. 定义允许的用户和受保护的文件类型。

   1. 定义完规则后，选择 " **添加**"。

1. 若要应用更改，请选择 " **保存**"。




## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>响应 "应更新自适应应用程序控制策略中的允许列表规则" 建议

如果安全中心的机器学习识别了以前未被允许的可能合法行为，你将看到此建议。 建议建议现有定义的新规则，以减少误报警报的数目。

要修正问题：

1. 在 "建议" 页上，选择 " **自适应应用程序控制策略中的允许列表规则" "应更新** " 建议，以查看具有新标识的潜在合法行为的组。

1. 选择包含要编辑的规则的组。

1. 如在[一组计算机上启用自适应应用程序控件](#enable-application-controls-on-a-group-of-machines)中所述，查看 "**配置应用程序控制规则**" 页的各个部分。

1. 若要应用更改，请选择 " **审核**"。




## <a name="audit-alerts-and-violations"></a>审核警报和冲突

1. 打开 Azure Defender 仪表板，并从 "高级保护" 区域中选择 " **自适应应用程序控件**"。

1. 若要查看具有最近警报的计算机的组，请查看 " **已配置** " 选项卡中列出的组。

1. 若要进一步调查，请选择组。

   ![最近的警报](./media/security-center-adaptive-application/recent-alerts.png)

1. 有关更多详细信息以及受影响的计算机的列表，请选择警报。



## <a name="move-a-machine-from-one-group-to-another"></a>将计算机从一个组移到另一个组

将计算机从一个组移到另一个组时，应用于该计算机的应用程序控制策略将更改为您将其移动到的组的设置。 你还可以将计算机从配置的组移动到未配置的组，这样做会删除应用于计算机的任何应用程序控制规则。

1. 打开 Azure Defender 仪表板，并从 "高级保护" 区域中选择 " **自适应应用程序控件**"。

1. 从 " **自适应应用程序控制** " 页上的 " **配置** " 选项卡中，选择包含要移动的计算机的组。

1. 打开  **已配置计算机**的列表。

1. 从行末尾的三个点打开计算机的菜单，然后选择 " **移动**"。 此时将打开 " **将计算机移动到不同的组** " 窗格。

1. 选择目标组，然后选择 " **移动计算机**"。

1. 若要保存更改，请选择 " **保存**"。





## <a name="manage-application-controls-via-the-rest-api"></a>通过 REST API 管理应用程序控件 

若要以编程方式管理自适应应用程序控件，请使用我们的 REST API。 

[此处](https://docs.microsoft.com/rest/api/securitycenter/adaptiveapplicationcontrols)提供了完整的 API 文档。

REST API 提供的一些函数：

* **List** 检索所有组的建议，并为每个组提供一个包含对象的 JSON。

* **Get** 检索包含完整建议数据 (的 JSON，即计算机列表、发布服务器/路径规则等) 。

* **Put** 配置规则 (使用检索 **到的 JSON 作为此** 请求的主体) 。
 
   > [!IMPORTANT]
   > **Put**函数需要的参数少于 Get 命令返回的 JSON 所包含的参数。
   >
   > 在 Put 请求中使用 JSON 之前，请删除以下属性： recommendationStatus、configurationStatus、问题、位置和 sourceSystem。




## <a name="next-steps"></a>后续步骤
本文档介绍了如何在 Azure 安全中心使用自适应应用程序控制来定义允许在 Azure 和非 Azure 计算机上运行的应用程序列表。 若要详细了解安全中心的某些其他云工作负荷保护功能，请参阅：

* [了解实时 (JIT) VM 访问](just-in-time-explained.md)
* [保护 Azure Kubernetes 群集](defender-for-kubernetes-introduction.md)