---
title: 使用 Azure 数据工厂从 SAP Business Warehouse 中加载数据 |Microsoft Docs
description: 使用 Azure 数据工厂从 SAP Business Warehouse (BW) 复制数据
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: jingwang
ms.openlocfilehash: 9458903378576a50db9be92b9377987829e1ba41
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200151"
---
# <a name="load-data-from-sap-business-warehouse-bw-by-using-azure-data-factory"></a>通过使用 Azure 数据工厂从 SAP Business Warehouse (BW) 加载数据

本指南向您演练如何使用数据工厂_将数据加载到 Azure 数据湖存储第 2 代的 SAP Business Warehouse (BW) 通过 Open Hub_。 可以遵循类似的步骤来将数据复制到其他[支持的接收器数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。 

> [!TIP]
> 请参阅[SAP BW Open Hub 连接器文章](connector-sap-business-warehouse-open-hub.md)一般情况下从 SAP BW 复制数据，其中包括介绍 SAP BW 开放中心集成和增量提取流。

## <a name="prerequisites"></a>必备组件

- **Azure 数据工厂 (ADF):** 如果没有数据工厂，请按照"[创建数据工厂](quickstart-create-data-factory-portal.md#create-a-data-factory)"部分，以创建一个。 

- **SAP BW 开放中心目标 (OHD) 与目标类型为"数据库表"。** 请按照[SAP BW Open Hub 目标配置](#sap-bw-open-hub-destination-configurations)部分创建一个，或若要确认是否现有 OHD 配置正确以与 ADF 集成。

- **正在使用的 SAP BW 用户需要具有以下权限：**

  - RFC 和 SAP BW 的授权。
  - 权限"**执行**"活动的授权对象"**S_SDSAUTH**"。

- **[自承载集成运行时](concepts-integration-runtime.md#self-hosted-integration-runtime)与 SAP.NET connector 3.0 所需**。 以下是需要执行的详细准备工作：

  1. 安装并注册自承载 IR 版本 > = 3.13 （下面的演练中介绍）。 

  2. 下载[64 位 SAP.NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)从 SAP 的网站，并将其安装自承载 IR 计算机上。  安装，在"可选设置步骤"窗口中，请确保选择"**程序集安装到 GAC**"选项下图所示。

     ![设置 SAP.NET 连接器](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="full-copy-from-sap-bw-open-hub"></a>从 SAP BW 打开中心的完整副本

在 Azure 门户中，转到数据工厂-> 选择**创作和监视**启动单独的选项卡中的 ADF UI。 

1. 在“开始使用”页中选择“复制数据”，启动“复制数据”工具。 

2. 上**属性**页上，指定的名称**任务名称**字段，然后选择**下一步**。

3. 上**源数据存储区**页上，单击 **+ 创建新的连接**-> 选择**SAP BW Open Hub**从连接器库-> 选择**继续**. 您可以筛选连接器在搜索框中键入"SAP"。

4. 上**指定 SAP BW Open Hub 连接**页上， 

   ![创建 SAP BW 开放中心链接服务](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. 选择**通过集成运行时连接**： 单击下拉列表选择现有的自承载 IR，或如果没有自承载 IR 尚未设置创建一个。 

      若要创建新的请单击 **+ 新建**下拉列表中-> 选择类型**自承载**-> 指定**名称**然后单击**下一步**-> 选择**Express 安装程序**当前计算机上安装或遵循**手动设置**存在的步骤。

      如中所述[先决条件](#prerequisites)，请确保你还有**SAP.NET connector 3.0**自承载 IR 正在其中运行在同一计算机上安装。

   2. 指定 SAP BW**服务器名称**，**系统编号**，**客户端 ID** **语言**（如果 EN 以外），**用户名**，并**密码**。

   3. 单击“测试连接”以验证设置，然后选择“完成”。

   4. 随即会显示新创建的连接。 选择“**下一步**”。

5. 上**选择 Open Hub 目标**页上，浏览打开的中心目标上将 SAP BW，并选择的一个想要复制数据，然后单击**下一步**。

   ![选择 SAP BW Open Hub 表](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. 如果需要请指定筛选器。 如果你 Open Hub 目标仅包含数据从单个数据传输进程 (DTP) 执行单个请求 id，或确定你 DTP 已完成并且想要复制的所有数据，请取消选中**排除上一次请求**。 你可以了解更多有关如何将这些设置与中的 SAP BW 配置[SAP BW Open Hub 目标配置](#sap-bw-open-hub-destination-configurations)部分。 单击**Validate**仔细检查数据返回，然后选择**下一步**。

   ![配置 SAP BW Open Hub 筛选器](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. 在**目标数据存储区**页上，单击 **+ 创建新的连接**，然后选择**Azure 数据湖存储第 2 代**，然后选择**继续**.

8. 在中**指定 Azure Data Lake 存储连接**页上， 

   ![创建 ADLS 第 2 代链接服务](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. 从"存储帐户名称"下拉列表中选择你的数据湖存储第 2 代的支持帐户。
   2. 选择“完成”以创建连接。 然后，选择“下一步”。

9. 在中**选择输出文件或文件夹**页上，输入"copyfromopenhub"作为输出文件夹的名称，然后选择**下一步**。

   ![选择输出文件夹](media/load-sap-bw-data/choose-output-folder.png)

10. 在中**文件格式设置**页上，选择**下一步**以使用默认设置。

    ![指定接收器格式](media/load-sap-bw-data/specify-sink-format.png)

11. 中**设置**页上，展开**性能设置**，并设置**复制度**例如若要从 SAP BW 负载并行 5。 单击“下一步”。

    ![配置复制设置](media/load-sap-bw-data/configure-copy-settings.png)

12. 在中**摘要**页上，查看设置，然后选择**下一步**。

13. 在中**部署**页上，选择**监视器**来监视管道。

    ![“部署”页](media/load-sap-bw-data/deployment.png)

14. 请注意，界面中已自动选择左侧的“监视”选项卡。 “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接：

    ![监视管道](media/load-sap-bw-data/pipeline-monitoring.png)

15. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。 该管道只包含一个活动（复制活动），因此只显示了一个条目。 若要切换回到管道运行视图，请选择顶部的“管道”链接。 选择“刷新”可刷新列表。

    ![活动监视](media/load-sap-bw-data/activity-monitoring.png)

16. 若要监视每个复制活动的执行详细信息，请在活动监视视图中选择“操作”下的“详细信息”链接（眼镜图标）。 可以监视详细信息，如从源复制到接收器的数据量、吞吐量、执行步骤以及相应的持续时间和使用的配置：

    ![活动监视的详细信息](media/load-sap-bw-data/activity-monitoring-details.png)

17. 审阅**最大请求 ID**复制。 返回到活动监视视图中，单击**输出**下**操作**。

    ![活动输出](media/load-sap-bw-data/activity-output.png)

    ![活动输出详细信息](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>从 SAP BW 打开中心的增量复制

> [!TIP]
>
> 请参阅[SAP BW Open Hub 连接器增量提取流](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow)若要了解有关 ADF SAP BW 开放中心连接器的工作方式将增量数据从 SAP BW 复制并阅读此文章从一开始，若要了解连接器相关的基础知识配置。

现在，让我们继续配置从 SAP BW 打开中心的增量复制。 

增量复制使用基于高水位线机制**请求 ID** SAP BW Open Hub 目标中自动生成通过 DTP。 此方法的工作流见下图：

![增量复制工作流](media/load-sap-bw-data/incremental-copy-workflow.png)

在 ADF UI**让我们开始吧**页上，选择**从模板创建管道**利用内置的模板。 

1. 搜索"SAP BW"查找和选择名为的模板**增量从 SAP BW 复制到 Azure 数据湖存储第 2 代**。 此模板将数据复制到 ADLS 第 2 代，更高版本可以遵循类似的流，以将复制到其他接收器类型。

2. 在模板主页上，选择或创建以下三个连接，然后选择**使用此模板**右下角。

   - **Azure Blob**： 在本演练中，我们使用 Azure Blob 存储高水印，这是最大复制的请求 id。
   - **SAP BW Open Hub**： 您从中复制数据的源。 请参阅前面的完整副本演练上详细配置。
   - **ADLS 第 2 代**： 将数据复制到接收器。 请参阅前面的完整副本演练上详细配置。

   ![从 SAP BW 模板进行增量复制](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. 此模板将生成具有三个活动的管道**查找、 复制数据和 Web** -，并使它们链接在一起上成功。 转到管道**参数**选项卡，你将看到你需要提供的所有配置。

   ![从 SAP BW 配置增量复制](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**： 指定要复制的数据的 Open Hub 表名称。

   - **ADLSGen2SinkPath**： 指定要将数据复制到的目标 ADLS 第 2 代路径。 如果该路径不存在，ADF 复制活动将创建一个在执行过程。

   - **HighWatermarkBlobPath**： 指定的路径，例如存储高水印值`container/path`。 

   - **HighWatermarkBlobName**： 指定 blob 名称，例如存储高水印值`requestIdCache.txt`。 在 blob 存储中，于 HighWatermarkBlobPath + HighWatermarkBlobName 的相应路径例如"*container/path/requestIdCache.txt*"，创建包含内容 0 的 blob。 

      ![Blob 内容](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**： 在此模板中，我们使用 Web 活动调用逻辑应用在 Blob 存储中设置高水印值。 或者，您可以使用 SQL 数据库，将其存储使用存储过程活动来更新的值。 

      在这里，您需要首先创建一个逻辑应用，如下所示，然后将复制**HTTP POST URL**对此字段。 

      ![逻辑应用配置](media/load-sap-bw-data/logic-app-config.png)

      1. 转到 Azure 门户-> 新建**逻辑应用**服务-> 单击 **+ 空白逻辑应用**若要转到**逻辑应用设计器**。

      2. 创建的触发器**收到时 HTTP 请求**。 指定的 HTTP 请求正文，如下所示：

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               },
               "type": "object"
            }
         }
         ```

      3. 添加的操作**创建的 blob**。 对于"文件夹路径"和"Blob 名称"，使用上述 HighWatermarkBlobPath 和 HighWatermarkBlobName 中配置的相同值。

      4. 单击**保存**，然后将复制的值**HTTP POST URL** ADF 管道中使用。

4. 为 ADF 管道参数提供的所有值后，可以单击**调试** -> **完成**调用运行验证配置。 或者，可以选择**全部发布**若要发布的所有更改，然后单击**触发器**执行运行。

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW Open Hub 目标配置

本部分介绍 SAP BW 端所需的配置，以便在 ADF 中使用 SAP BW 开放中心连接器将数据复制。

### <a name="configure-delta-extraction-in-sap-bw"></a>在 SAP BW 中配置增量提取

如果您需要历史记录副本和增量复制或仅增量复制，请在 SAP BW 中配置增量提取。

1. 创建 Open Hub 目标 (OHD)

   可以在 SAP 事务 RSA1，会自动创建所需的转换和数据传输进程 (DTP) 中创建 OHD。 使用以下设置：

   - 对象类型可以是任何。 此处我们使用 InfoCube 作为示例。
   - **目标类型：***数据库表*
   - **表的键：***技术密钥*
   - **提取：***到表中保留数据和插入记录*

   ![创建 SAP BW OHD 增量提取](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![create-sap-bw-ohd-delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   可能会增加并行 DTP 运行 SAP 工作进程数：

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. 计划中进程链 DTP

   时，多维数据集增量 DTP 才起作用所需的行不压缩尚未。 因此，必须确保 BW 多维数据集压缩未运行之前 DTP, 到打开的中心表。 为此的最简单方法将此 DTP 集成到现有的进程链。 在下面的示例中，插入在步骤之间的进程链 （到 OHD) DTP 调整 （聚合汇总） 和折叠 （多维数据集压缩）。

   ![create-sap-bw-process-chain](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>在 SAP BW 中配置完整提取

除了增量提取，可能想要具有相同 InfoProvider 完整提取。 它通常适用于你想要执行完整而无需增量复制或您希望[重新同步增量提取](#re-sync-delta-extraction)。

不能为同一 OHD 多个 DTP。 因此，您需要创建其他 OHD 然后增量提取。

![create-sap-bw-ohd-full](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

对于完全加载 OHD，选择增量提取的选项不同选项：

- 在 OHD： 设置为"提取"选项"*删除数据和插入记录*"。 否则会很多时候时重复 DTP BW 进程链中的提取数据。

- 在 DTP： 将"提取模式"设置为"*完整*"。 只需创建 OHD 后，必须更改从增量为 Full 的自动创建的 DTP:

   ![create-sap-bw-ohd-full2](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- 在 ADF SAP BW Open Hub 连接器： 请关闭选项"*排除上一次请求*"。 否则会提取执行任何操作。 

您通常手动运行完整 DTP。 或您可能会为完整 DTP 创建进程链-它通常是独立于现有的进程链的单独的进程链。 在任一情况下，您必须**确保 DTP 已完成后再启动使用 ADF 复制提取**，否则，将复制部分数据。

### <a name="run-delta-extraction-the-first-time"></a>运行增量提取第一次

从技术上讲是第一个增量提取**完整提取**。 复制数据时，注意默认 ADF SAP BW Open Hub 连接器不包括最后一个请求。 在第一次，ADF 复制活动中的增量提取的情况下没有数据将被提取直到后续 DTP 生成单独的请求 id。 表中的增量数据 尽管有两种可行的方法来避免这种情况：

1. 关闭选项"排除上一次请求"的第一个增量提取中的这种情况下，您需要确保在开始第一次的增量提取之前已完成第一个增量 DTP
2. 使用重新同步增量提取，如下所述的过程。

### <a name="re-sync-delta-extraction"></a>重新同步增量提取

有几种方案的更改在 SAP BW 多维数据集中的数据，但并不认为通过增量 DTP:

- SAP BW 选择性删除 （的使用任何筛选器条件的行）
- （错误请求） 的 SAP BW 请求删除

SAP Open Hub 目标不是 （在所有 SAP BW 的支持包自 2015 年） 的数据市场控制数据目标。 因此，就可以从多维数据集删除数据，而无需更改 OHD 中的数据。 在这种情况下，则必须重新同步在 ADF 中的数据的多维数据集数据，通过执行以下步骤：

1. 运行完整提取在 ADF 中 （通过使用在 SAP 中完整 DTP）
2. 有关增量 DTP 删除打开的中心表中的所有行
3. 增量 DTP 的状态设置为已提取

在此之后，所有后续增量 DTPs 和 ADF 增量提取正常按预期方式工作。

通过运行增量 DTP 手动使用以下选项，可以将增量 DTP 的状态设置为已提取："*无数据传输;源中的增量状态：提取*"。

## <a name="next-steps"></a>后续步骤

请转到以下文章，了解有关 SAP BW Open Hub 连接器支持： 

> [!div class="nextstepaction"]
>[SAP 业务仓库 Open Hub 连接器](connector-sap-business-warehouse-open-hub.md)
