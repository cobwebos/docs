---
title: 将远程监视解决方案与 Azure Data Lake Store 集成 | Microsoft Docs
description: 了解如何使用 Azure 流分析作业将远程监视解决方案与 Azure Data Lake Store 集成。
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 5ba9b5534e986be1cbe55043a9acdd981d2ed7fd
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971731"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>将远程监视解决方案与 Azure Data Lake Store 集成

远程监视解决方案有时无法满足一些高级分析要求。 Azure Data Lake Store 非常适合此类应用程序，因为它可以存储大量不同数据集中的数据，并能与 Azure Data Lake Analytics 集成，以提供按需分析。

在本操作指南中，我们要使用 Azure 流分析作业将数据从远程监视解决方案中的 IoT 中心流式传输到 Azure Data Lake Store。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要满足以下条件：

* [部署远程监视解决方案加速器](iot-accelerators-remote-monitoring-deploy.md)。
  * 远程监视解决方案会将本文中使用的 IoT 中心和 Azure 流分析作业部署到 Azure 订阅。
* [部署 Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * 应将 Data Lake Store 部署到远程监视解决方案所在的同一区域。
  * 在帐户中[创建](../data-lake-store/data-lake-store-get-started-portal.md#createfolder)名为“streaming”的文件夹。

## <a name="create-a-consumer-group"></a>创建使用者组

在远程监视解决方案的 IoT 中心内创建专用的使用者组。 流分析作业将使用此组向 Data Lake Store 流式传输数据。

> [!NOTE]
> 应用程序使用使用者组从 Azure IoT 中心提取数据。 应该为每五个输出使用者创建一个新的使用者组。 最多可以创建 32 个使用者组。

1. 登录到 Azure 门户。

1. 在 Azure 门户中，单击“Cloud Shell”按钮。

    ![门户启动图标](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. 执行以下命令创建新的使用者组：

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> 使用远程监视解决方案中的资源组和 IoT 中心名称。

## <a name="create-stream-analytics-job"></a>创建流分析作业

创建一个 Azure 流分析作业，以便将数据从 IoT 中心流式传输到 Azure Data Lake Store。

1. 单击“创建资源”，从市场中选择“物联网”，然后单击“流分析作业”。

    ![新建流分析作业](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. 输入作业名称，并选择适当的订阅和资源组。

1. 在 Data Lake Store 所在的同一区域或其附近选择一个位置。 本文使用“美国东部”。

1. 请务必将“宿主环境”保留为默认值“云”。

1. 单击“创建”。

    ![创建流分析作业](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>配置流分析作业

1. 转到远程监视解决方案资源组中的“流分析作业”。

1. 在“概述”页上单击“输入”。

    ![概述页](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. 单击“添加流输入”，从下拉列表中选择“IoT 中心”。

    ![添加输入](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. 在“新建输入”选项卡上，提供 **IoTHub** 输入别名。

1. 在“使用者组”下拉列表中，选择前面创建的使用者组。 本文使用 **streamanalyticsjob**。

    ![选择输入](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. 单击“ **保存**”。

1. 在“概述”页上单击“输出”。

    ![添加 Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. 单击“添加”，从下拉列表中选择“Data Lake Store”。

    ![添加输出](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. 在“新建输出”选项卡上，输入 **DataLakeStore** 输出别名。

1. 选择在前面步骤中创建的 Data Lake Store 帐户并提供文件夹结构，以将数据流式传输到存储中。

1. 在“日期格式”字段中，输入 **/streaming/{date}/{time}**。 保留默认日期格式 YYYY/MM/DD 和时间格式 HH。

    ![提供文件夹结构](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. 单击“授权”。

    必须授权 Data Lake Store 向流分析作业授予对文件系统的写入访问权限。

    ![向 Data Lake Store 提供流分析授权](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    随后会出现一个弹出窗口。弹出窗口关闭时，完成授权后“授权”按钮会灰显。

    > [!NOTE]
    > 如果弹出窗口中显示错误，请以 Incognito 模式打开新浏览器窗口，然后重试。

1. 单击“ **保存**”。

## <a name="edit-the-stream-analytics-query"></a>编辑流分析查询

Azure 流分析使用类似于 SQL 的查询语言来指定用于流式传输数据的输入源、按需转换该数据，并输出到各种存储或处理目标。

1. 在“概述”选项卡上，单击“编辑查询”。

    ![编辑查询](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. 在查询编辑器中，将 [YourOutputAlias] 和 [YourInputAlias] 占位符替换为前面定义的值。

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![流分析查询](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. 单击“ **保存**”。
1. 单击“是”接受更改。

## <a name="start-the-stream-analytics-job"></a>启动流分析作业

1. 在“概述”选项卡上，单击“启动”。

    ![启动流分析作业](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. 在“启动作业”选项卡上，单击“自定义”。

1. 将自定义时间往回设置几个小时，以便从设备开始流式传输时开始拾取数据。

1. 单击“启动”。

    ![选择自定义日期](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    等到作业进入运行中状态，如果看到可以来自查询的错误，请务必检查语法是否正确。

    ![作业正在运行](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    流作业开始从 IoT 中心读取数据，并将数据存储在 Data Lake Store 中。 可能需要在几分钟后，数据才会开始显示在 Data Lake Store 中。

## <a name="explore-the-streaming-data"></a>浏览流数据

1. 转到 Data Lake Store。

1. 在“概述”选项卡上，单击“数据资源管理器”。

1. 在数据资源管理器中，向下钻取到 **/streaming** 文件夹。 将会看到以 YYYY/MM/DD/HH 格式创建的文件夹。

    ![浏览流数据](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    将会看到一些 json 文件，每小时各有一个文件。

    ![浏览流数据](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>后续步骤

Azure Data Lake Analytics 可用于针对 Data Lake Store 数据集执行大数据分析。 请在 [Data Lake Analytics 文档](https://docs.microsoft.com/azure/data-lake-analytics)中了解详细信息。
