---
title: Azure 安全中心内的文件完整性监视（预览版）| Microsoft Docs
description: " 了解如何在 Azure 安全中心启用文件完整性监视。 "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: terrylan
ms.openlocfilehash: 722a4fd11f35f04ed22d73638f07d15c49ea3c26
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161830"
---
# <a name="file-integrity-monitoring-in-azure-security-center-preview"></a>Azure 安全中心内的文件完整性监视（预览版）
使用本演练了解如何在 Azure 安全中心配置文件完整性监视 (FIM)。

## <a name="what-is-fim-in-security-center"></a>安全中心内的 FIM 是什么？
文件完整性监视 (FIM) 也称为更改监视，它可以检查操作系统、应用程序软件和其他组件的文件和注册表，确定它们是否发生了可能表示遭受攻击的更改。 将使用比较方法来确定当前文件状态是否不同于上次扫描该文件时的状态。 可以利用这种比较来确定文件是否发生了有效或可疑的修改。

安全中心的文件完整性监视会验证 Windows 文件、Windows 注册表和 Linux 文件的完整性。 选择要通过启用 FIM 来监视的文件。 启用 FIM 后，安全中心会监视针对文件执行的活动，例如：

- 文件和注册表的创建与删除
- 文件修改（文件大小、访问控制列表和内容哈希的更改）
- 注册表修改（大小、访问控制列表、类型和内容的更改）

安全中心会建议要监视的、可以轻松对其启用 FIM 的实体。 你也可以定义自己的 FIM 策略或要监视的实体。 本演练会演示这些操作。

> [!NOTE]
> 文件完整性监视 (FIM) 功能适用于 Windows 和 Linux 计算机与 VM，已在安全中心的标准层上提供。 若要详细了解安全中心的定价层，请参阅[定价](security-center-pricing.md)。
FIM 将数据上传到 Log Analytics 工作区。 需要根据上传的数据量支付数据费用。 请参阅 [Log Analytics 定价](https://azure.microsoft.com/pricing/details/log-analytics/)了解详细信息。
>
>

> [!NOTE]
> FIM 使用 Azure 更改跟踪解决方案来跟踪和识别环境中发生的更改。 启用文件完整性监视后，会获得一个“解决方案”类型的“更改跟踪”资源。 如果删除该“更改跟踪”资源，则会禁用安全中心内的文件完整性监视功能。
>
>

## <a name="which-files-should-i-monitor"></a>应监视哪些文件？
在选择要监视的文件时，应考虑对系统和应用程序至关重要的文件。 考虑选择预期不会在计划外发生更改的文件。 选择应用程序或操作系统经常更改的文件（例如日志文件和文本文件）会造成很多的干扰，使攻击识别变得很困难。

安全中心会根据包含文件和注册表更改的已知攻击模式，建议默认情况下应该监视的文件。

## <a name="using-file-integrity-monitoring"></a>使用文件完整性监视
1. 打开“安全中心”仪表板。
2. 在左窗格中的“高级云防御”下，选择“文件完整性监视”。
![安全中心仪表板][1]

此时会打开“文件完整性监视”。
  ![安全中心仪表板][2]

为每个工作区提供了以下信息：

- 过去一周发生的更改总数（如果未对该工作区启用 FIM，可能会看到短划线“-”）
- 向工作区报告的计算机和 VM 总数
- 工作区的地理位置
- 工作区所在的 Azure 订阅

可能还会显示工作区对应的以下按钮：

- ![启用图标][3] 指示未对工作区启用 FIM。 选择工作区可在该工作区中的所有计算机上启用 FIM。
- ![升级计划图标][4]指示工作区或订阅不是在安全中心的标准层下运行。 若要使用 FIM 功能，订阅必须运行标准层。  选择工作区可以升级到标准层。 若要详细了解标准层以及升级方法，请参阅[升级到安全中心的标准层以增强安全性](security-center-pricing.md)。
- 空白（没有任何按钮）表示已对工作区启用 FIM。

在“文件完整性监视”下，可以选择某个工作区以便对其启用 FIM、查看该工作区的“文件完整性监视”仪表板工作区，或者将该工作区[升级](security-center-pricing.md)到标准层。

## <a name="enable-fim"></a>启用 FIM
若要对工作区启用 FIM：

1. 在“文件完整性监视”下，选择附带“启用”按钮的工作区。
2. 此时会打开“启用文件完整性监视”，其中显示了该工作区中的 Windows 和 Linux 计算机数目。

   ![启用文件完整性监视][5]

   此外，还会列出适用于 Windows 和 Linux 的建议设置。  展开“Windows 文件”、“注册表”和“Linux 文件”查看建议项的完整列表。

3. 请取消选中不想要对其应用 FIM 的所有建议实体。
4. 选择“应用文件完整性监视”以启用 FIM。

> [!NOTE]
> 随时可以更改设置。 请参阅下面的[编辑受监视的实体](security-center-file-integrity-monitoring.md#edit-monitored-items)来了解详细信息。
>
>

## <a name="view-the-fim-dashboard"></a>查看 FIM 仪表板
“文件完整性监视”仪表板显示已启用 FIM 的工作区。 对工作区启用 FIM 后或者在“文件完整性监视”窗口中选择已启用 FIM 的工作区时，FIM 仪表板将会打开。

![文件完整性监视仪表板][6]

工作区的 FIM 仪表板显示以下信息：

- 连接到该工作区的计算机总数
- 所选时间段内发生的更改总数
- 更改类型（文件、注册表）的细分
- 更改类别的（修改、添加、删除）细分

选择仪表板顶部的“筛选器”可以应用要查看其更改的时间段。

![时间段筛选器][7]

“计算机”选项卡（如上所示）列出向此工作区报告的所有计算机。 对于每台计算机，仪表板将会列出：

- 所选时间段内发生的更改总数
- 更改总数的细分，以文件更改数或注册表更改数的形式列出

在搜索字段中输入计算机名，或选择“计算机”选项卡下列出的计算机时，“日志搜索”将会打开。日志搜索结果显示所选时间段内对该计算机所做的所有更改。 可以展开某项更改以查看其详细信息。

![日志搜索][8]

“更改”选项卡（如下所示）列出所选时间段内对该工作区所做的所有更改。 对于更改的每个实体，仪表板将会列出：

- 发生更改的计算机
- 更改类型（注册表或文件）
- 更改类别（修改、添加、删除）。
- 更改日期和时间

![工作区的更改][9]

在搜索字段中输入某项更改，或选择“更改”选项卡下列出的某个实体时，“更改详细信息”将会打开。

![更改详细信息][10]

## <a name="edit-monitored-entities"></a>编辑受监视的实体

1. 返回到“文件完整性监视”仪表板并选择“设置”。

  ![设置][11]

  此时会打开“工作区配置”，其中显示了三个选项卡：“Windows 注册表”、“Windows 文件”和“Linux 文件”。 每个选项卡列出可在该类别中编辑的实体。 对于列出的每个实体，安全中心会指出是已启用 (true) 还是未启用 (false) FIM。  编辑实体可以启用或禁用 FIM。

  ![工作区配置][12]

2. 选择一个 identityprotection。 在此示例中，我们选择了“Windows 注册表”下的某个项。 此时会打开“更改跟踪的编辑”。

  ![编辑或更改跟踪][13]

在“更改跟踪的编辑”下，可以：

- 启用 (True) 或禁用 (False) 文件完整性监视
- 提供或更改实体名称
- 提供或更改值或路径
- 删除实体、丢弃更改或保存更改

## <a name="add-a-new-entity-to-monitor"></a>添加要监视的新实体
1. 返回到“文件完整性监视”仪表板并选择顶部的“设置”。 此时会打开“工作区配置”。
2. 在“工作区配置”下，选择要添加的实体类型对应的选项卡：“Windows 注册表”、“Windows 文件”或“Linux 文件”。 在此示例中，我们选择了“Linux 文件”。

  ![添加要监视的新项][14]

3. 选择 **添加** 。 此时会打开“更改跟踪的添加”。

  ![输入请求的信息][15]

4. 在“添加”页上，键入请求的信息并选择“保存”。

## <a name="disable-monitored-entities"></a>禁用受监视的实体
1. 返回到“文件完整性监视”仪表板。
2. 选择当前已启用 FIM 的工作区。 如果某个工作区缺少“启用”按钮或“升级计划”按钮，则表示该工作区已启用 FIM。

  ![选择已启用 FIM 的工作区][16]

3. 在“文件完整性监视”下，选择“设置”。

  ![选择设置][17]

4. 在“工作区配置”下，选择“已启用”设置为 true 的某个组。

  ![工作区配置][18]

5. 在“更改跟踪的编辑”窗口中，将“已启用”设置为 False。

  ![将“已启用”设置为 false][19]

6. 选择“保存”。

## <a name="disable-fim"></a>禁用 FIM
可以禁用 FIM。 FIM 使用 Azure 更改跟踪解决方案来跟踪和识别环境中发生的更改。 禁用 FIM 会从所选工作区中删除“更改跟踪”解决方案。

1. 若要禁用 FIM，请返回到“文件完整性监视”仪表板。
2. 选择工作区。
3. 在“文件完整性监视”下，选择“禁用”。

  ![禁用 FIM][20]

4. 选择“删除”以禁用 FIM。

## <a name="next-steps"></a>后续步骤
本文已介绍如何在安全中心使用文件完整性监视 (FIM)。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [设置安全策略](security-center-policies.md) -- 了解如何为 Azure 订阅和资源组配置安全策略。
* [管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助你保护 Azure 资源。
* [安全运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和应对安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和应对安全警报。
* [监视合作伙伴解决方案](security-center-partner-solutions.md) -- 了解如何监视合作伙伴解决方案的运行状况。
* [安全中心常见问题解答](security-center-faq.md) -- 查找有关服务用法的常见问题的解答。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/) - 获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
