---
title: 使用 Azure 数据工厂从 SAP 业务仓库加载数据
description: 使用 Azure 数据工厂从 SAP Business Warehouse (BW) 复制数据
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: jingwang
ms.openlocfilehash: 0c96ecff27a57b3277e7c8105766059b739d11af
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672653"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP Business Warehouse 复制数据

本文介绍如何使用 Azure 数据工厂通过 Open Hub 将 SAP Business Warehouse (BW) 中的数据复制到 Azure Data Lake Storage Gen2。 可以使用类似的过程将数据复制到其他[受支持的接收器数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。

> [!TIP]
> 有关如何从 SAP BW 复制数据的一般信息，包括 SAP BW Open Hub 集成和增量提取流程，请参阅[使用 Azure 数据工厂通过 Open Hub 复制 SAP Business Warehouse 中的数据](connector-sap-business-warehouse-open-hub.md)。

## <a name="prerequisites"></a>先决条件

- **Azure 数据工厂**：如果没有，请按照[创建数据工厂](quickstart-create-data-factory-portal.md#create-a-data-factory)的步骤进行操作。

- **SAP BW 开放目标类型为 "数据库表" 的中心目标（OHD）** ：若要创建 OHD 或检查是否为数据工厂集成正确配置了 OHD，请参阅本文的[SAP BW 开放式中心目标配置](#sap-bw-open-hub-destination-configurations)部分。

- **SAP BW 用户需要以下权限**：

  - 远程函数调用 (RFC) 和 SAP BW 的授权。
  - 对 **S_SDSAUTH** 授权对象拥有“执行”活动权限。

- **一个[自承载集成运行时 (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) 和 SAP .NET 连接器 3.0**。 遵循以下安装步骤：

  1. 安装并注册自承载集成运行时 3.13 或更高版本。 （本文稍后会介绍此过程。）

  2. 从 SAP 网站下载 [64 位 SAP Connector for Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html)，并将其安装到自承载 IR 所在的同一台计算机上。 在安装期间，请务必在“可选安装步骤”对话框中选择“将程序集安装到 GAC”，如下图所示：

     ![“安装 SAP. NET 连接器”对话框](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>从 SAP BW Open Hub 执行完整复制

在 Azure 门户中转到你的数据工厂。 选择“创作和监视”，在单独的选项卡中打开数据工厂 UI。

1. 在“开始”页上选择“复制数据”，打开“复制数据”工具。

2. 在“属性”页上指定一个**任务名称**，然后选择“下一步”。

3. 在“源数据存储”页上，单击“+创建新连接”。 从连接器库中选择“SAP BW Open Hub”，然后选择“继续”。 若要筛选连接器，可在搜索框中键入 **SAP**。

4. 在“指定 SAP BW Open Hub 连接”页上，执行以下步骤创建新连接。

   ![创建 SAP BW Open Hub 链接服务页](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. 在“通过集成运行时进行连接”列表中，选择一个现有的自承载 IR。 如果你没有自承载 IR，请创建一个。

      若要创建新的自承载 IR，请依次选择“+新建”、“自承载”。 输入**名称**，然后选择“下一步”。 选择“快速安装”以在当前计算机上安装，或遵循提供的“手动设置”步骤。

      如[先决条件](#prerequisites)中所述，请确保在运行自承载 IR 的同一台计算机上安装 SAP Connector for Microsoft .NET 3.0。

   2. 填写 SAP BW 的“服务器名称”、“系统编号”、“客户端 ID”、“语言”（如果不是“EN”）、“用户名”和“密码”。

   3. 选择“测试连接”以验证设置，然后选择“完成”。

   4. 现已创建新的连接。 选择“**下一步**”。

5. 在“选择 Open Hub 目标”页上，浏览 SAP BW 中可用的 Open Hub 目标。 选择要从中复制数据的 OHD，然后选择“下一步”。

   ![选择 SAP BW Open Hub 目标表](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. 如果需要，请指定筛选器。 如果你的 OHD 仅包含采用单个请求 ID 的单个数据传输进程 (DTP) 执行中的数据，或者你确定 DTP 已完成并想要复制数据，请清除“排除最后一个请求”复选框。

   请在本文的 [SAP BW Open Hub 目标配置](#sap-bw-open-hub-destination-configurations)部分详细了解这些设置。 选择“验证”以仔细检查要返回的数据。 然后，选择“下一步”。

   ![配置 SAP BW Open Hub 筛选器](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. 在“目标数据存储”页上，选择“+创建新连接” **“Azure Data Lake Storage Gen2”** “继续”。 >  > 

8. 在“指定 Azure Data Lake Storage 连接”页上，遵循这些步骤来创建连接。

   ![创建 ADLS Gen2 链接服务页](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. 在“名称”下拉列表中，选择支持 Data Lake Storage Gen2 的帐户。
   2. 选择“完成”以创建连接。 然后，选择“下一步”。

9. 在“选择输出文件或文件夹”页上，输入 **copyfromopenhub** 作为输出文件夹名称。 然后，选择“下一步”。

   ![选择输出文件夹页](media/load-sap-bw-data/choose-output-folder.png)

10. 在“文件格式设置”页上，选择“下一步”以使用默认设置。

    ![指定接收器格式页](media/load-sap-bw-data/specify-sink-format.png)

11. 在“设置”页上，展开“性能设置”。 为“复制并行度”输入一个值（例如 5），表示从 SAP BW 加载数据的并行度。 然后，选择“下一步”。

    ![配置复制设置](media/load-sap-bw-data/configure-copy-settings.png)

12. 在“摘要”页上复查设置。 然后，选择“下一步”。

13. 在“部署”页上，选择“监视”以监视管道。

    ![“部署”页](media/load-sap-bw-data/deployment.png)

14. 请注意，页面左侧的“监视”选项卡已自动选择。 “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接。

    ![管道监视视图](media/load-sap-bw-data/pipeline-monitoring.png)

15. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”。 该管道只包含一个活动（复制活动），因此只显示了一个条目。 若要切换回到管道运行视图，请选择顶部的“管道”链接。 选择“刷新”可刷新列表。

    ![活动监视屏幕](media/load-sap-bw-data/activity-monitoring.png)

16. 若要监视每个复制活动的执行详细信息，请在活动监视视图中选择“操作”下面的“详细信息”链接（眼镜图标）。 提供的详细信息包括从源复制到接收器的数据量、数据吞吐量、执行步骤和持续时间，以及使用的配置。

    ![活动监视详细信息](media/load-sap-bw-data/activity-monitoring-details.png)

17. 若要查看“最大请求 ID”，请返回到活动监视视图，并选择“操作”下的“输出”。

    ![活动输出屏幕](media/load-sap-bw-data/activity-output.png)

    ![活动输出详细信息视图](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>从 SAP BW Open Hub 执行增量复制

> [!TIP]
> 请参阅 [SAP BW Open Hub 连接器增量提取流程](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow)，了解数据工厂中的 SAP BW Open Hub 连接器如何从 SAP BW 复制增量数据。 此文还可帮助你了解基本的连接器配置。

现在，让我们继续配置从 SAP BW Open Hub 执行的增量复制。

增量复制使用基于**请求 ID** 的“高水印”机制。 该 ID 是由 DTP 在 SAP BW Open Hub 目标中自动生成的。 下图演示了此工作流：

![增量复制工作流程图](media/load-sap-bw-data/incremental-copy-workflow.png)

在数据工厂的“开始”页上，选择“从模板创建管道”以使用内置模板。

1. 搜索 **SAP BW**，找到并选择“从 SAP BW 增量复制到 Azure Data Lake Storage Gen2”模板。 此模板将数据复制到 Azure Data Lake Storage Gen2。 可以使用类似的工作流将数据复制到其他接收器类型。

2. 在模板的主页上，选择或创建以下三个连接，然后选择窗口右下角的“使用此模板”。

   - **Azure blob 存储**：在本演练中，我们使用 Azure blob 存储来存储高水印，这是*复制的最大请求 ID*。
   - **SAP BW 打开中心**：这是要从中复制数据的源。 有关详细配置，请参考前面的完整复制演练。
   - **Azure Data Lake Storage Gen2**：这是要向其复制数据的接收器。 有关详细配置，请参考前面的完整复制演练。

   ![从 SAP BW 模板执行增量复制](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. 此模板将生成具有以下三个活动的管道，并将其链接到成功：*查找*、*复制数据*和*Web*。

   中转到 "管道**参数**" 选项卡。你将看到需要提供的所有配置。

   ![从 SAP BW 配置执行增量复制](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**：指定要从中复制数据的打开的中心表名称。

   - **ADLSGen2SinkPath**：指定要向其复制数据的目标 Azure Data Lake Storage Gen2 路径。 如果该路径不存在，则数据工厂的复制活动将在执行期间创建一个路径。

   - **HighWatermarkBlobPath**：指定用于存储高水印值的路径，如 `container/path`。

   - **HighWatermarkBlobName**：指定用于存储高水印值的 blob 名称，如 `requestIdCache.txt`。 在 Blob 存储中，转到 HighWatermarkBlobPath+HighWatermarkBlobName 的相应路径，例如 *container/path/requestIdCache.txt*。 创建包含内容 0 的 Blob。

      ![Blob 内容](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**：在此模板中，我们使用 WebActivity 调用 Azure 逻辑应用来设置 Blob 存储中的高水印值。 或者，可以使用 Azure SQL 数据库来存储该值。 使用存储过程活动来更新该值。

      必须先创建逻辑应用，如下图所示。 然后，粘贴 **HTTP POST URL**。

      ![逻辑应用配置](media/load-sap-bw-data/logic-app-config.png)

      1. 转到 Azure 门户。 选择一个新的“逻辑应用”服务。 选择“+空白逻辑应用”转到“逻辑应用设计器”。

      2. 创建“收到 HTTP 请求时”触发器。 按如下所示指定 HTTP 请求正文：

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. 添加“创建 Blob”操作。 对于“文件夹路径”和“Blob 名称”，请使用前面在 **HighWatermarkBlobPath** 和 **HighWatermarkBlobName** 中配置的相同值。

      4. 选择“保存”。 然后，复制“HTTP POST URL”的值以便在数据工厂管道中使用。

4. 提供数据工厂的管道参数后，选择“调试” **“完成”以调用一个运行来验证配置。**  >  或者，选择“全部发布”以发布更改，然后选择“触发器”以执行运行。

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW Open Hub 目标配置

本部分介绍如何在 SAP BW 端进行配置，以使用数据工厂中的 SAP BW Open Hub 连接器来复制数据。

### <a name="configure-delta-extraction-in-sap-bw"></a>在 SAP BW 中配置增量提取

如果需要历史副本和增量副本，或者只需要增量副本，请在 SAP BW 中配置增量提取。

1. 创建 Open Hub 目标。 可以在 SAP Transaction RSA1 中创建 OHD，这会自动创建所需的转换和数据传输过程。 使用以下设置：

   - **ObjectType**：可以使用任何对象类型。 此处我们使用 **InfoCube** 作为示例。
   - **目标类型**：选择**数据库表**。
   - **表键**：选择 "**技术密钥**"。
   - **提取**：选择 "**保留数据" 并将记录插入到表中**。

   ![“创建 SAP BW OHD 增量提取”对话框](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![“创建 SAP BW OHD 增量提取”对话框 2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   可能提高 DTP 的并行运行 SAP 工作进程数：

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. 在进程链中计划 DTP。

   仅当所需的行未压缩时，针对多维数据集的增量 DTP 才能正常工作。 在对 Open Hub 表运行 DTP 之前，请确保 BW 多维数据集压缩未运行。 为此，最简单的方法是将 DTP 集成到现有的进程链中。 在以下示例中，DTP（针对 OHD）已插入到进程链中的“调整”（聚合汇总）与“折叠”（多维数据集压缩）步骤之间。

   ![创建 SAP BW 进程链的流程图](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>在 SAP BW 中配置完整提取

除了增量提取以外，还可以配置同一 SAP BW InfoProvider 的完整提取。 若要执行完整复制而不是增量复制，或者要[重新同步增量提取](#resync-delta-extraction)，则你通常会这样做。

不能对同一个 OHD 使用多个 DTP。 因此，在执行增量提取之前，必须先创建一个附加的 OHD。

![创建 SAP BW OHD 完整提取](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

对于满负载 OHD，请选择不同于增量提取的选项：

- 在 OHD 中：设置**提取**选项以**删除数据和插入记录**。 否则，在 BW 进程链中重复 DTP 时，会多次提取数据。

- 在 DTP：将**提取模式**设置为 "**完整**"。 在创建 OHD 后，必须紧接着将自动创建的 DTP 从“增量”更改为“完整”，如下图所示：

   ![用于创建配置了“完整”提取的 SAP BW OHD 的对话框](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- 在数据工厂的 BW 开放式集线器连接器中：关闭 "**排除最后一个请求**"。 否则不会提取任何内容。

你通常会手动运行完整 DTP。 或者，可为完整 DTP 创建一个进程链。 它通常是一个与现有进程链不相关的独立链。 对于任一做法，请确保在使用数据工厂复制活动开始提取之前已完成 DTP。 否则，只会复制部分数据。

### <a name="run-delta-extraction-the-first-time"></a>首次运行增量提取

从技术上讲，首次增量提取属于完整提取。 默认情况下，SAP BW Open Hub 连接器在复制数据时会排除最后一个请求。 对于首次增量提取，在后续 DTP 在具有不同请求 ID 的表中生成增量数据之前，数据工厂复制活动不会提取任何数据。 可通过两种方式来避免这种情况：

- 针对首次增量提取禁用“排除最后一个请求”选项。 在首次启动增量提取之前，确保已完成首次增量 DTP。
-  使用下一部分所述的重新同步增量提取的过程。

### <a name="resync-delta-extraction"></a>重新同步增量提取

出现以下情况时，会更改 SAP BW 多维数据集中的数据，但增量 DTP 将忽略此类情况：

- SAP BW 选择性删除行（使用任何筛选条件）
- SAP BW 请求删除（错误的请求）

SAP Open Hub 目标不是受数据市场控制的数据目标（自 2015 年开始已包含在所有 SAP BW 支持包中）。 因此，无需更改 OHD 中的数据即可删除多维数据集中的数据。 然后，必须将多维数据集的数据与数据工厂重新同步：

1. 在数据工厂中运行完整提取（使用 SAP 中的完整 DTP）。
2. 删除增量 DTP 的 Open Hub 表中的所有行。
3. 将增量 DTP 的状态设置为“已提取”。

然后，所有后续增量 DTP 和数据工厂增量提取都会按预期方式工作。

若要将增量 DTP 的状态设置为“已提取”，可以使用以下选项来手动运行增量 DTP：

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>后续步骤

了解 SAP BW Open Hub 连接器支持：

> [!div class="nextstepaction"]
>[SAP Business Warehouse Open Hub 连接器](connector-sap-business-warehouse-open-hub.md)
