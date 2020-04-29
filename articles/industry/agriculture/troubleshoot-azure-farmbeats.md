---
title: 排查 Azure FarmBeats 问题
description: 本文介绍了如何对 Azure FarmBeats 进行故障排除。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a40c64910260a2d63a529d25e1089fb618fcec1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113492"
---
# <a name="troubleshoot"></a>疑难解答

本文提供常见 Azure FarmBeats 问题的解决方案。 有关更多帮助，请与我们的farmbeatssupport@microsoft.com[支持论坛](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)联系或通过电子邮件发送。

> [!NOTE]
  > 如果你在4月的时间内安装了 FarmBeats，但你的作业失败并出现空的错误消息，则你的安装可能没有分配任何批处理配额来确定对关键运行状况和安全组织的支持。 有关详细信息，请参阅[此处](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/)。 需要将 Vm 分配给批处理帐户才能成功运行作业。

## <a name="install-issues"></a>安装问题

  > [!NOTE]
  > 如果由于错误而重新启动安装，请确保在重新触发安装之前删除**资源组**或删除资源组中的所有资源。

### <a name="invalid-sentinel-credentials"></a>Sentinel 凭据无效

安装期间提供的 Sentinel 凭据不正确。 请用正确的凭据重新启动安装。

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>已达到指定订阅的 Batch 帐户的区域帐户配额

增加配额，或删除未使用的批处理帐户，然后重新启动安装。

### <a name="invalid-resource-group-location"></a>资源组位置无效

确保**资源组**与安装过程中指定的**区域**位于同一位置。

### <a name="other-install-issues"></a>其他安装问题

请联系我们，并提供以下详细信息：

- 你的订阅 ID
- 资源组名称
- 按照以下步骤附加日志文件，以进行部署失败：

    1. 导航到 Azure 门户中的**资源组**。

    2. 选择左侧的 "**设置**" 部分下的 "**部署**"。

    3. 对于每个显示**失败**的部署，选择 "到详细信息" 并下载部署详细信息。 将此文件附加到邮件。

## <a name="sensor-telemetry"></a>传感器遥测

### <a name="cant-view-telemetry-data"></a>无法查看遥测数据

**症状**：设备或传感器已部署，并且你已将 FarmBeats 与设备合作伙伴链接，但无法在 FarmBeats 上获取或查看遥测数据。

**纠正措施**

1. 请参阅 FarmBeats Datahub 资源组。
2. 选择**事件中心**（DatafeedEventHubNamespace），然后检查传入消息数。
3. 执行下列任一操作：

   - 如果没有*传入消息*，请联系你的设备伙伴。  
   - 如果有*传入消息*，请联系我们，并提供 Datahub 和加速器日志并捕获遥测数据。

若要了解如何下载日志，请参阅["手动收集日志"](#collect-logs-manually)部分。  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>从传感器引入历史/流式传输数据后，无法查看遥测数据

**症状**：设备或传感器已部署，并且你已在 FarmBeats 和引入遥测上为 EventHub 创建了设备/传感器，但无法在 FarmBeats 上获取或查看遥测数据。

**纠正措施**

1. 请确保已正确完成合作伙伴注册-可以通过转到 datahub swagger 来检查此项，导航到/Partner API，执行 Get 操作并检查伙伴是否已注册。 否则，请按照以下[步骤](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)添加合作伙伴。

2. 确保使用了正确的遥测消息格式：

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":"<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>没有 Azure 事件中心连接字符串

**纠正措施**

1. 在 Datahub Swagger 中转到合作伙伴 API。
2. 选择 "**获取** > " "**尝试** > **执行**"。

> [!NOTE]
> 你感兴趣的传感器伙伴的合作伙伴 ID。

3. 返回到合作伙伴 API，并选择 "**获取/\<ID>**"。
4. 指定步骤3中的合作伙伴 ID，然后选择 "**执行**"。

   API 响应应包含事件中心连接字符串。

### <a name="device-appears-offline"></a>设备显示脱机

**症状**：设备已安装，并且你已将 FarmBeats 链接到设备伙伴。 设备处于联机状态并发送遥测数据，但它们显示为脱机状态。

**纠正操作**未为此设备配置报告间隔。 若要设置报表间隔，请与设备制造商联系。 

### <a name="error-deleting-a-device"></a>删除设备时出错

删除设备时，可能会遇到以下常见错误方案之一：  

**消息**： "传感器中引用了设备：有一个或多个传感器与设备关联。 删除传感器，然后删除设备。 "  

**含义**：设备与在场中部署的多个传感器相关联。

**纠正措施**  

1. 通过加速器删除与设备关联的传感器。  
2. 如果要将传感器与其他设备关联，请要求设备伙伴执行相同操作。  
3. 使用`DELETE API`调用删除设备，并将 force 参数设置为*true*。  

**消息**： "设备在设备中被引用为 ParentDeviceId：有一个或多个设备作为子设备与此设备关联。 请删除它们，然后删除此设备。 "  

**意思**：你的设备具有与之关联的其他设备。  

**纠正措施**

1. 删除与此特定设备关联的设备。  
2. 删除特定设备。  

    > [!NOTE]
    > 如果传感器与设备相关联，则无法删除该设备。 有关如何删除关联的传感器的详细信息，请参阅[从传感器合作伙伴获取传感器数据](get-sensor-data-from-sensor-partner.md)中的**删除传感器**部分。
    > 合作伙伴没有删除设备或传感器的权限。 只有管理员有权删除。

## <a name="issues-with-jobs"></a>作业问题

### <a name="farmbeats-internal-error"></a>FarmBeats 内部错误

**消息**： "FarmBeats 内部错误，请参阅故障排除指南以了解更多详细信息。"

**纠正操作**此问题可能是由于数据管道中的暂时性故障导致的。 请重新创建该作业。 如果错误仍然存在，请联系我们并提供错误消息/日志。

## <a name="accelerator-troubleshooting"></a>加速器疑难解答

### <a name="access-control"></a>访问控制

**问题**：你在添加角色分配时收到错误。

**消息**： "找不到匹配的用户"。

**纠正操作**检查要为其添加角色分配的电子邮件 ID。 电子邮件 ID 必须与在 Active Directory 中为该用户注册的 ID 完全匹配。 如果错误仍然存在，请联系我们并提供错误消息/日志。

### <a name="unable-to-log-in-to-accelerator"></a>无法登录到加速器

**消息**： "错误：你无权调用服务。 请与管理员联系以获取授权。 "

**纠正操作**要求管理员授权你访问 FarmBeats 部署。 为此，可通过在快捷键的 "**设置**" 窗格中执行 RoleAssignment Api 或访问控制来完成。  

如果你已获得访问权限并遇到此错误，请通过刷新页面重试。 如果错误仍然存在，请联系我们并提供错误消息/日志。

![项目 FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>加速器问题  

**问题**：您收到了一个加速器错误，导致无法确定的原因。

**消息**： "错误：发生未知错误。"

**纠正操作**如果使页面空闲时间太长，则会出现此错误。 刷新页面。 如果错误仍然存在，请联系我们并提供错误消息/日志。

**问题**： FarmBeats 加速器不显示最新版本，即使在升级 FarmBeatsDeployment 之后。

**纠正操作**此错误发生的原因是，浏览器中存在服务辅助角色。 请执行以下操作：

1. 关闭所有已打开加速器的浏览器选项卡，然后关闭浏览器窗口。
2. 启动浏览器的新实例，并重新加载加速器 URI。 此操作将加载新版本的加速器。

## <a name="sentinel-imagery-related-issues"></a>Sentinel：与图像相关的问题

### <a name="wrong-username-or-password"></a>错误的用户名或密码

**作业失败消息**： "需要完全身份验证才能访问此资源"。

**纠正操作**：执行以下操作之一：

- 请使用以下步骤，使用正确的用户名/密码更新 FarmBeats，然后重试该作业。

  **更新 Sentinel 用户名**

    1. 登录到 [Azure 门户](https://portal.azure.com)。
    2. 在**搜索**框中，搜索 FarmBeats Datahub 资源组。
    3. 选择存储帐户存储 * * * * >**容器** > **batch-prep-files** > **to_vm** > **配置 .ini**文件
    4. 选择 "**编辑**"
    5. 更新 sentinel_account 部分中的用户名

  **更新 Sentinel 密码**

    1. 登录到 [Azure 门户](https://portal.azure.com)。
    2. 在**搜索**框中，搜索 FarmBeats Datahub 资源组。
    3. 选择 keyvault-* * * * *
    4. 选择 "设置" 下的访问策略
    5. 选择**添加访问策略**
    6. 使用**机密管理**从模板配置，并将自己添加到主体
    7. 选择 "**添加**"，然后在 "**访问策略**" 页上选择 "**保存**"
    8. 选择**设置**下的**机密**
    9. 选择 " **Sentinel-密码**"
    10. 创建值的新版本并启用它。

- 重新运行失败的作业，或在5到7天的日期范围内运行附属索引作业，然后查看作业是否成功。

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel hub：错误的 URL 或站点不可访问

**作业失败消息**： "糟糕，出现了问题。 您尝试访问的页面暂时不可用。 "

**纠正操作**：

1. 在浏览器中打开[Sentinel](https://scihub.copernicus.eu/dhus/) ，查看网站是否可访问。
2. 如果无法访问该网站，请检查任何防火墙、公司网络或其他阻止软件是否阻止访问网站，然后执行必要的步骤以允许使用 Sentinel URL。 
3. 重新运行失败的作业，或在5到7天的日期范围内运行附属索引作业，然后检查作业是否成功。  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel 服务器：按下进行维护

**作业失败消息**： "Copernicus 开放式访问中心即将恢复！ 抱歉，我们目前正在进行维护。 稍后我们将重新联机！ " 

**纠正操作**：

如果在 Sentinel 服务器上执行了任何维护活动，则会出现此问题。

1. 如果任何作业或管道由于正在执行维护而失败，请在一段时间后重新提交作业。 

   有关任何计划内或非计划的 Sentinel 维护活动的信息，请访问[Copernicus 开放式 Access Hub 新闻](https://scihub.copernicus.eu/news/)站点。  

2. 重新运行失败的作业，或在5到7天的日期范围内运行附属索引作业，然后检查作业是否成功。

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel：达到了最大连接数

**作业失败消息**： "用户名>" 实现\<的两个并发流的最大数目。 "

**意思**：如果由于已达到连接的最大数量而导致作业失败，则会在多个作业中使用同一个 Sentinel 帐户。

**纠正操作**：尝试下列任一操作：

* 等待其他作业完成，然后重新运行失败的作业。
* 创建新的 Sentinel 帐户，并在 FarmBeats 中更新 "Sentinel 用户名" 和 "密码"。

### <a name="sentinel-server-refused-connection"></a>Sentinel 服务器：已拒绝连接

**作业失败消息**： "服务器拒绝连接： http://172.30.175.69:8983/solr/dhus"。

**纠正操作**：如果在 Sentinel 服务器上执行了任何维护活动，则会出现此问题。

1. 如果任何作业或管道由于正在执行维护而失败，请在一段时间后重新提交作业。

   有关任何计划内或非计划的 Sentinel 维护活动的信息，请访问[Copernicus 开放式 Access Hub 新闻](https://scihub.copernicus.eu/news/)站点。  

2. 重新运行失败的作业，或在5到7天的日期范围内运行附属索引作业，然后检查作业是否成功。

### <a name="soil-moisture-map-has-white-areas"></a>土料湿气地图具有空白区域

**问题**：生成了**土料湿气地图**，但该地图主要包含空白区域。

**纠正操作**：如果为请求映射的时间生成的卫星索引的 NDVI 值小于0.3，则会出现此问题。 有关详细信息，请访问[Sentinel 中的技术指南](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)。

1. 请针对不同的日期范围重新运行该作业，并检查卫星索引中的 NDVI 值是否大于0.3。

## <a name="collect-logs-manually"></a>手动收集日志

[安装并部署 Azure 存储资源管理器]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>收集 Datahub 中的 Azure 数据工厂作业日志或应用服务日志

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在**搜索**框中，搜索 FarmBeats Datahub 资源组。
3. 在**资源组**仪表板上，搜索*datahublogs\* *存储帐户。 例如， *datahublogsmvxmq*。  
4. 在 "**名称**" 列中，选择要查看**存储**帐户仪表板的存储帐户。
5. 在 " **datahubblogs\* ** " 窗格中，选择 "**在资源管理器中打开**" 以查看**打开的 Azure 存储资源管理器**应用程序。
6. 在左窗格中，选择 " **Blob 容器**"，然后选择 "适用于 Azure 数据工厂的**作业**日志" 或 "Appinsights" 作为应用服务日志的**日志**。
7. 选择 "**下载**" 并将日志下载到计算机上的本地文件夹。

    ![项目 FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>为加速器收集 Azure 数据工厂作业日志或应用服务日志

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在**搜索**框中，搜索 FarmBeats 加速器资源组。
3. 在**资源组**仪表板上，搜索*存储\* *存储帐户。 例如， *storagedop4k\**。
4. 在 "**名称**" 列中选择存储帐户，查看**存储帐户**仪表板。
5. 在 "**存储\* ** " 窗格中，选择 "**在资源管理器中打开**" 以打开 Azure 存储资源管理器应用程序。
6. 在左窗格中，选择 " **Blob 容器**"，然后选择 "适用于 Azure 数据工厂的**作业**日志" 或 "Appinsights" 作为应用服务日志的**日志**。
7. 选择 "**下载**" 并将日志下载到计算机上的本地文件夹。

## <a name="high-cpu-usage"></a>CPU 使用率较高

**错误**：收到一个表示**CPU 使用率较高**的电子邮件警报。

**纠正操作**：

1. 请参阅 FarmBeats Datahub 资源组。
2. 选择**应用服务**。  
3. 请参阅向上缩放[应用服务定价页](https://azure.microsoft.com/pricing/details/app-service/windows/)，然后选择适当的定价层。
