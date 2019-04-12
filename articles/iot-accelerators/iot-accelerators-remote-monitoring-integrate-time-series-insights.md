---
title: 将时序见解与远程监视集成 - Azure | Microsoft Docs
description: 在本操作指南中，你将学习如何为尚未包含时序见解的现有远程监视解决方案配置时序见解。
author: aditidugar
manager: timlt
ms.author: adugar
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 850d8bbb525763e0e7d0c0441173180b7c469dd8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085144"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>将 Azure 时序见解与远程监视集成

Azure 时序见解是一种完全托管的分析、存储和可视化服务，用于管理云中的 IoT 时序数据。 可利用时序见解存储和管理时序数据，同时探索和可视化事件，还可进行根因分析，对多个站点和资产进行比较。

远程监视解决方案加速器现可与时序见解进行自动部署和集成。 在本操作指南中，你将学习如何为尚未包含时序见解的现有远程监视解决方案配置时序见解。

> [!NOTE]
> 时序见解目前在 Azure 中国云中不可用。 Azure 中国云中的新远程监视解决方案加速器部署使用 Cosmos DB 进行所有存储。

## <a name="prerequisites"></a>先决条件

要完成本操作指南，需要已部署远程监视解决方案：

* [部署远程监视解决方案加速器](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>创建使用者组

在 IoT 中心创建一个专用的使用者组，用于将数据流式传输到时序见解。

> [!NOTE]
> 应用程序使用使用者组从 Azure IoT 中心提取数据。 每个使用者组最多允许五个输出使用者。 应为每 5 个输出接收器创建一个新的使用者组，最多可以创建 32 个使用者组。

1. 在 Azure 门户中，单击“Cloud Shell”按钮。

1. 执行以下命令创建新的使用者组。 在远程监控部署中使用 IoT 中心的名称，并将远程监控部署的名称用作资源组名称：

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>部署时序见解

接下来，将时序见解作为附加资源部署到远程监视解决方案中，并将其连接到 IoT 中心。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择“创建资源” > “物联网” > “时序见解”。

    ![新建时序见解](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. 若要创建时序见解环境，请使用下表中的值：

    | 设置 | 值 |
    | ------- | ----- |
    | 环境名称 | 以下屏幕截图使用名称“contorosrmtsi”。 完成此步骤后，请选择自己的唯一名称。 |
    | 订阅 | 在下拉列表中选择自己的 Azure 订阅。 |
    | 资源组 | **使用现有项**。 选择现有远程监视资源组的名称。 |
    | 位置 | 本文使用“美国东部”。 在远程监视解决方案所在的同一区域中创建环境（若可能）。 |
    | SKU |**S1** |
    | 容量 | **1** |

    ![创建时序见解](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. 单击“创建”。 可能需要花费片刻时间来创建环境。

## <a name="create-event-source"></a>创建事件源

创建新事件源，以连接到 IoT 中心。 确保使用前面步骤中创建的使用者组。 时序见解要求每个服务具有未被其他服务使用的专用使用者组。

1. 导航到新的时序见解环境。

1. 在左侧选择“事件源”。

    ![查看事件源](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. 单击“添加”。

    ![添加事件源](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. 若要将 IoT 中心配置为新事件源，请使用下表中的值：

    | 设置 | 值 |
    | ------- | ----- |
    | 事件源名称 | 以下屏幕截图使用名称“contosorm-iot-hub”。 完成此步骤后，请使用自己的唯一名称。 |
    | 源 | **IoT 中心** |
    | 导入选项 | **从可用的订阅使用 IoT 中心** |
    | 订阅 ID | 在下拉列表中选择自己的 Azure 订阅。 |
    | IoT 中心名称 | **contosorma57a6**。 使用远程监视解决方案中的 IoT 中心名称。 |
    | IoT 中心策略名称 | iothubowner 确保使用的策略是所有者策略。 |
    | IoT 中心策略密钥 | 此字段会自动填充。 |
    | IoT 中心使用者组 | **timeseriesinsights** |
    | 事件序列化格式 | **JSON**     | 
    | 时间戳属性名称 | 留空 |

    ![创建事件源](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. 单击“创建”。

## <a name="configure-the-data-access-policy"></a>配置数据访问策略

要确保有权访问远程监视解决方案的所有用户都能够在时序见解资源管理器中浏览数据，请在 Azure 门户中的数据访问策略下添加应用程序和用户。 

1. 在导航列表中，选择“资源组”。

1. 选择“ContosoRM”资源组。

1. 在 Azure 资源列表中选择“contosormtsi”。

1. 选择“数据访问策略”，以查看当前的角色分配列表。

1. 选择“添加”以打开“选择用户规则”窗格。

   如果无权分配角色，则不会看到“添加”选项。

1. 在“角色”下拉列表中选择一个角色，例如“读者”和“参与者”。

1. 在“选择”列表中，选择用户、组或应用程序。 如果没有在列表中看到安全主体，则可在“选择”框中键入相应内容，以便在目录中搜索显示名称、电子邮件地址和对象标识符。

1. 选择“保存”，创建角色分配。 片刻之后，会在数据访问策略中为安全主体分配角色。

> [!NOTE]
> 如果需要向其他用户授予对时序见解资源管理器的访问权限，可以使用这些步骤来[授予数据访问权限](../time-series-insights/time-series-insights-data-access.md#grant-data-access)。

## <a name="configure-azure-stream-analytics"></a>配置 Azure 流分析 

下一步将配置 Azure 流分析管理器微服务以停止向 Cosmos DB 发送消息并仅将其存储在时序见解中。 如果希望在 Cosmos DB 中复制消息，请跳过该步骤。

1. 在导航列表中，选择“资源组”。

1. 选择“ContosoRM”资源组。

1. 在资源列表中查找 Azure 流分析 (ASA) 流式处理作业。 资源名称以“streamingjobs-”开头。

1. 在顶部，请单击按钮以停止 ASA 流式处理作业。

1. 编辑 ASA 查询并删除“SELECT”、“INTO”和“FROM”子句，这些子句指向 Cosmos DB 中的消息流。 这些子句应位于查询的底部，如以下示例所示：

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. 重启 Azure 流分析流式处理作业。

7. 通过在命令提示符处键入以下命令，将最新更改拉取到 Azure 流分析管理器微服务：

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>配置遥测微服务

通过在命令提示符中键入以下命令拉取最新遥测微服务：

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>“[可选]”配置 Web UI 以链接到时序见解资源管理器

要在时序见解资源管理器中轻松查看数据，建议自定义 UI，以轻松链接到环境。 为此，请使用以下命令将最新更改拉取到 Web UI：

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>配置环境变量

要完成时序见解集成，将需要为已更新的微服务配置部署环境。

### <a name="basic-deployments"></a>基本部署

为已更新的微服务配置 `basic` 部署的环境。

1. 在 Azure 门户的左侧导航面板中，单击“Azure Active Directory”图标。

1. 单击“应用注册”。

1. 搜索并单击“ContosoRM”应用程序。

1. 导航到“设置” > “密钥”并为应用程序创建新密钥。 请确保将密钥值复制到安全位置。

1. 使用最新标记从 GitHub 存储库中拉取[最新 docker compose yaml 文件](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm)。 

1. 按照[如何创建和使用 SSH 密钥](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)中列出的步骤将 SSH 添加到 VM。

1. 连接后，请键入 `cd /app`。

1. 将以下环境变量添加到 docker compose yaml 文件中的每个微服务和 VM 中的 `env-setup` 脚本：

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. 导航到“遥测服务”并通过添加上述相同的环境变量来编辑 docker compose 文件。

1. 导航到“ASA 管理器服务”并通过添加 `PCS_TELEMETRY_STORAGE_TYPE` 编辑 docker compose 文件。

1. 使用 VM 中的 `sudo ./start.sh` 重启 docker 容器。

### <a name="standard-deployments"></a>标准部署

为上面已更新的微服务配置 `standard` 部署的环境

1. 在命令行上，运行 `kubectl proxy`。 有关详细信息，请参阅 [访问 Kubernetes API](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server)。

1. 打开 Kubernetes 管理控制台。

1. 找到配置映射以为 TSI 添加以下新环境变量：

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. 编辑遥测服务 Pod 的模板 yaml 文件：

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. 编辑 ASA 管理器服务 Pod 的模板 yaml 文件：

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>后续步骤

* 若要了解如何在时序见解资源管理器浏览数据和诊断警报，请参阅[执行根本原因分析](iot-accelerators-remote-monitoring-root-cause-analysis.md)教程。

* 若要了解如何在时序见解资源管理器中浏览和查询数据，请参阅 [Azure 时序见解资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)文档。
