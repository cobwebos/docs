---
title: "使用 Power BI Embedded 迁移工具 | Microsoft Docs"
description: "可以使用 Power BI Embedded 迁移工具将报表从 Power BI 工作区集合复制到 Power BI Embedded。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 0b7b5089045daf6dd88fcd84e316b2bd44f8c927
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-power-bi-embedded-migration-tool"></a>使用 Power BI Embedded 迁移工具

可以使用 Power BI Embedded 迁移工具将报表从 Power BI 工作区集合复制到 Power BI Embedded。

将内容从工作区集合迁移到 Power BI 服务可与当前解决方案并行进行，不会造成任何停机。

## <a name="limitations"></a>限制

* 已推送的数据集不可下载，需要使用 Power BI 服务的 Power BI REST API 重新创建。
* 在 2016 年 11 月 26 日之前导入的 PBIX 文件不可下载。

## <a name="download"></a>下载

可以从 [GitHub](https://github.com/Microsoft/powerbi-migration-sample) 下载迁移工具示例。 可以下载存储库的 zip 文件，或者将其克隆到本地。 下载后，可在 Visual Studio 中打开 *powerbi-migration-sample.sln*，以生成并运行迁移工具。

## <a name="migration-plans"></a>迁移计划

迁移计划只是元数据，为 Power BI 工作区集合中的内容编目，并描述如何将这些内容发布到 Power BI Embedded。

### <a name="start-with-a-new-migration-plan"></a>从新的迁移计划开始

迁移计划是 Power BI 工作区集合中提供的、以后需要移到 Power BI Embedded 的元数据。 迁移计划以 XML 文件的形式存储。

让我们先创建一个新的迁移计划。 若要创建新的迁移计划，请执行以下操作。

1. 选择“文件” > “新建迁移计划”。

    ![新建迁移计划](media/migrate-tool/migrate-tool-plan.png)

2. 在“选择 Power BI 工作区集合资源组”对话框中，选择“环境”下拉菜单，并选择“生产”。

3. 系统会提示登录。 使用自己的 Azure 订阅登录名。

    > [!IMPORTANT]
    > 这**不是**用来登录到 Power BI 的 Office 365 组织帐户。

4. 选择存储 Power BI 工作区集合资源的 Azure 订阅。

    ![选择 Azure 订阅](media/migrate-tool/migrate-tool-select-resource-group.png)

5. 在订阅列表下面，选择包含自己的工作区集合的**资源组**，并选择“选择”。

    ![选择资源组](media/migrate-tool/migrate-tool-select-resource-group2.png)

6. 选择“分析”。 这样就能获得 Azure 订阅中的项的清单，从而可以开始创建计划。

    ![选择“分析”按钮](media/migrate-tool/migrate-tool-analyze-group.png)

    > [!NOTE]
    > 分析过程可能需要几分钟时间，具体取决于工作区集合的数量以及工作区集合中的内容量。

7. **分析**完成后，会提示保存迁移计划。

此时，已将迁移计划连接到 Azure 订阅。 请阅读下文，了解迁移计划的处理流程。 这包括“分析和规划迁移”、“下载”、“创建组”和“上传”。

### <a name="save-your-migration-plan"></a>保存迁移计划

可以保存迁移计划以备后用。 这会创建一个包含迁移计划中所有信息的 XML 文件。

若要保存迁移计划，请执行以下操作。

1. 选择“文件” > “保存迁移计划”。

    ![“保存迁移计划”菜单选项](media/migrate-tool/migrate-tool-save-plan.png)

2. 为文件命名或使用系统生成的文件名，选择“保存”。

### <a name="open-an-existing-migration-plan"></a>打开现有的迁移计划

可以打开已保存的迁移计划，以继续处理迁移。

若要打开现有的迁移计划，请执行以下操作。

1. 选择“文件” > “打开现有的迁移计划”。

    ![“打开现有的迁移计划”菜单选项](media/migrate-tool/migrate-tool-open-plan.png)

2. 选择迁移文件，并选择“打开”。

## <a name="step-1-analyze-and-plan-migration"></a>步骤 1：分析和规划迁移

“分析和规划迁移”选项卡提供 Azure 订阅的资源组中当前内容的视图。

![“分析和规划迁移”选项卡](media/migrate-tool/migrate-tool-step1.png)

我们以 *SampleResourceGroup* 为例。

### <a name="paas-topology"></a>PaaS 拓扑

这是“资源组”>“工作区集合”>“工作区”的列表。 资源组和工作区集合显示友好名称。 工作区会显示 GUID。

列表中的项还显示一种颜色，以及一个采用 (#/#) 格式的数字。 这表示可下载的报表数。

黑色表示可下载所有报表。 红色表示无法下载某些报表。 左侧的数字表示可下载的报表总数。 右侧的数字表示组中的报表总数。

选择 PaaS 拓扑中的某个项可在报表部分中显示报表。

### <a name="reports"></a>报告

报表部分列出可用报表，并指示相应的报表是否可下载。

![Power BI 工作区集合中的报表列表](media/migrate-tool/migrate-tool-analyze-reports.png)

### <a name="target-structure"></a>目标结构

在“目标结构”中，可以告知工具要将内容下载到何处，以及如何上传内容。

#### <a name="download-plan"></a>下载计划

系统会自动创建路径。 如果需要，可以更改此路径。 如果确实更改了路径，则需要选择“更新路径”。

**实际上这不会执行下载**， 而只是指定要将报表下载到的位置的结构。

#### <a name="upload-plan"></a>上传计划

在此处，可以指定要在 Power BI 服务中创建的应用工作区所用的前缀。 之后，该前缀将成为 Azure 中存在的工作区的 GUID。

![指定组名称前缀](media/migrate-tool/migrate-tool-upload-plan.png)

**实际上这不会在 Power BI 服务中创建组**， 而只是定义组的命名结构。

如果更改前缀，则需要选择“生成上传计划”。

如果需要，可以右键单击某个组，并选择直接在上传计划中为该组重命名。

![“重命名组”上下文菜单选项](media/migrate-tool/migrate-tool-upload-report-rename-item.png)

> [!NOTE]
> 组的名称不能包含空格或无效字符。

## <a name="step-2-download"></a>步骤 2：下载

在“下载”选项卡中，可以看到报表和关联元数据的列表。 此外，还可以看到导出状态以及先前的导出状态。

![“下载”选项卡](media/migrate-tool/migrate-tool-download-tab.png)

可以使用两个选项。

* 选择特定的报表，并选择“下载选定内容”
* 选择“全部下载”。

![“下载选定内容”按钮](media/migrate-tool/migrate-tool-download-options.png)

成功下载后，会看到“完成”状态，并看到存在 PBIX 文件的反映。

下载完成后，请选择“创建组”选项卡。

## <a name="step-3-create-groups"></a>步骤 3：创建组

下载可用的报表后，可以转到“创建组”选项卡。在此选项卡中，可以根据所创建的迁移计划在 Power BI 服务中创建应用工作区。 这会使用在“分析和规划迁移”所述的“上传”选项卡中提供的名称创建应用工作区。

![“创建组”选项卡](media/migrate-tool/migrate-tool-create-groups.png)

若要创建应用工作区，可以选择“创建选定组”或“创建所有缺少的组”。

选择其中任一选项时，系统会提示登录。 *请使用想要在其中创建应用工作区的 Power BI 服务的凭据。*

![Power BI 登录屏幕](media/migrate-tool/migrate-tool-create-group-sign-in.png)

这会在 Power BI 服务中创建应用工作区， 但不会将报表上传到应用工作区。

可以通过登录到 Power BI 并验证应用工作区是否存在，来验证是否已创建该工作区。 可以看到，工作区中没有任何内容。

![Power BI 服务中的应用工作区](media/migrate-tool/migrate-tool-app-workspace.png)

创建工作区后，可以转到“上传”选项卡。

## <a name="step-4-upload"></a>步骤 4：上传

在“上传”选项卡中，可将报表上传到 Power BI 服务。 将会看到已在“下载”选项卡中下载的报表列表，以及基于迁移计划的目标组名称。

![“上传”选项卡](media/migrate-tool/migrate-tool-upload-tab.png)

可以上传选定的报表，或上传所有报表。 还可以重置上传状态，以重新上传项。

此外，可以使用相应的选项来选择当存在同名的报表时要采取的操作。 可以选择“中止”、“忽略”或“覆盖”。

![报表存在时要采取的操作选项下拉菜单](media/migrate-tool/migrate-tool-upload-report-same-name.png)

![上传选定的结果](media/migrate-tool/migrate-tool-upload-selected.png)

### <a name="duplicate-report-names"></a>重复报表名称

如果存在同名的报表，但已知它是一份不同的报表，则需要更改报表的 **TargetName**。 可以通过手动编辑迁移计划 XML 来更改名称。

需要关闭迁移工具进行更改，然后重新打开工具和迁移计划。

在上面的示例中，某个已克隆的报表发生失败，指出存在同名的报表。 查看迁移计划 XML 会看到以下信息。

```
<ReportMigrationData>
    <PaaSWorkspaceCollectionName>SampleWorkspaceCollection</PaaSWorkspaceCollectionName>
    <PaaSWorkspaceId>4c04147b-d8fc-478b-8dcb-bcf687149823</PaaSWorkspaceId>
    <PaaSReportId>525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe</PaaSReportId>
    <PaaSReportLastImportTime>1/3/2017 2:10:19 PM</PaaSReportLastImportTime>
    <PaaSReportName>cloned</PaaSReportName>
    <IsPushDataset>false</IsPushDataset>
    <IsBoundToOldDataset>false</IsBoundToOldDataset>
    <PbixPath>C:\MigrationData\SampleResourceGroup\SampleWorkspaceCollection\4c04147b-d8fc-478b-8dcb-bcf687149823\cloned-525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe.pbix</PbixPath>
    <ExportState>Done</ExportState>
    <LastExportStatus>OK</LastExportStatus>
    <SaaSTargetGroupName>SampleMigrate</SaaSTargetGroupName>
    <SaaSTargetGroupId>6da6f072-0135-4e6c-bc92-0886d8aeb79d</SaaSTargetGroupId>
    <SaaSTargetReportName>cloned</SaaSTargetReportName>
    <SaaSImportState>Failed</SaaSImportState>
    <SaaSImportError>Report with the same name already exists</SaaSImportError>
</ReportMigrationData>
```

针对失败的项，可以更改 SaaSTargetReportName 的名称。

```
<SaaSTargetReportName>cloned2</SaaSTargetReportName>
```

然后，可在迁移工具中重新打开计划，并上传失败的报表。

返回 Power BI 后可以看到，报表和数据集已上传到应用工作区中。

![Power BI 服务中的报表列表](media/migrate-tool/migrate-tool-upload-app-workspace.png)

<a name="upload-local-file"></a>
### <a name="upload-a-local-pbix-file"></a>上传本地 PBIX 文件

可以上传 Power BI Desktop 文件的本地版本。 必须关闭工具、编辑 XML，并在 **PbixPath** 属性中放置本地 PBIX 的完整路径。

```
<PbixPath>[Full Path to PBIX file]</PbixPath>
```

编辑 XML 后，在迁移工具中重新打开计划，并上传报表。

<a name="directquery-reports"></a>
### <a name="directquery-reports"></a>DirectQuery 报表

需要更新 DirectQuery 报表的连接字符串。 可在 *powerbi.com* 中执行此操作，或以编程方式查询来自 Power BI Embedded (Paas) 的连接字符串。 有关示例，请参阅[提取 PaaS 报表中的 DirectQuery 连接字符串](migrate-code-snippets.md#extract-directquery-connection-string-from-power-bi-workspace-collections)。

然后，可以更新 Power BI 服务中数据集的连接字符串，并设置数据源的凭据。 可以查看以下示例来了解如何执行此操作。

* [在 Power BI Embedded 中更新 DirectQuery 连接字符串](migrate-code-snippets.md#update-directquery-connection-string-in-power-bi-embedded)
* [在 Power BI Embedded 中设置 DirectQuery 凭据](migrate-code-snippets.md#set-directquery-credentials-in-power-bi-embedded)

## <a name="next-steps"></a>后续步骤

将报表从 Power BI 工作区集合迁移到 Power BI Embedded 之后，接下来可以更新应用程序中，开始在此应用工作区中嵌入报表。

有关详细信息，请参阅[如何将 Power BI 工作区集合内容迁移到 Power BI Embedded](migrate-from-power-bi-workspace-collections.md)。

有更多问题？ [尝试向 Power BI 社区提问](http://community.powerbi.com/)