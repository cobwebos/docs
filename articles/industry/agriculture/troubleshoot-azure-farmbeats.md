---
title: 排查 Azure FarmBeats 问题
description: 本文介绍如何排查 Azure FarmBeats 问题。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 110fef2dcb4c1581d49fcb776276ebd9c4ae2235
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497057"
---
# <a name="troubleshoot-azure-farmbeats"></a>排查 Azure FarmBeats 问题

本文提供常见 Azure FarmBeats 问题的解决方案。 有关更多帮助，请通过[支持论坛](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)联系我们，或通过 farmbeatssupport@microsoft.com 向我们发送电子邮件。

> [!NOTE]
  > 如果你在 4 月安装了 FarmBeats，但作业失败且错误消息为空，则你的安装可能尚未分配有任何批处理配额，无法优先支持关键运行状况和安全组织。 有关详细信息，请参阅 [此处](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) 。 需要请求将 VM 分配给 Batch 帐户才能成功运行作业。

## <a name="install-issues"></a>安装问题

  > [!NOTE]
  > 如果因出错而要重新开始安装，请确保删除资源组，或在重新触发安装之前删除资源组中的所有资源。

### <a name="invalid-sentinel-credentials"></a>Sentinel 凭据无效

安装期间提供的 Sentinel 凭据错误。 请用正确的凭据重新开始安装。

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>已达到指定订阅的 Batch 帐户的区域帐户配额

增加配额或删除未使用的 Batch 帐户，然后重新开始安装。

### <a name="invalid-resource-group-location"></a>资源组位置无效

确保资源组与安装期间指定的区域位于同一位置 。

### <a name="other-install-issues"></a>其他安装问题

请联系我们并提供以下详细信息：

- 订阅 ID
- 资源组名称
- 按照以下步骤附加部署失败的日志文件：

    1. 在 Azure 门户中，导航到“资源组”。

    2. 选择左侧“设置”部分下的“部署” 。

    3. 对于每个显示“失败”的部署，请选择“查看详细信息”，然后下载部署详细信息。 将此文件附加到邮件。

## <a name="sensor-telemetry"></a>传感器遥测数据

### <a name="cant-view-telemetry-data"></a>无法查看遥测数据

**故障描述**：设备或传感器已部署，并且你已将 FarmBeats 关联到设备合作伙伴，但无法获取或查看 FarmBeats 上的遥测数据。

**纠正措施**

1. 中转到 FarmBeats 资源组。
2. 选择**事件中心**命名空间（"传感器-伙伴-eh-名称-名称"），单击 "事件中心"，然后检查分配给伙伴的事件中心内的传入消息数。
3. 执行下列任一操作：

   - 如果没有传入消息，请联系你的设备合作伙伴。  
   - 如果有传入消息，请联系我们，并提供数据中心日志、加速器日志以及捕获到的遥测数据。

要了解如何下载日志，请转到[“手动收集日志”](#collect-logs-manually)部分。  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>从传感器引入历史/流数据后无法查看遥测数据

**故障描述**：设备或传感器已部署，你已在 FarmBeats 上创建设备/传感器，并将遥测数据引入到 EventHub 中，但无法获取或查看 FarmBeats 上的遥测数据。

**纠正措施**

1. 确保合作伙伴注册正确 - 可转到 Datahub Swagger、导航到“/合作伙伴 API”、执行获取操作并查看合作伙伴是否已注册来检查此情况。 如果未正确注册，请按照以下[步骤](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)来添加合作伙伴。

2. 确保已使用正确的遥测消息格式：

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
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>没有 Azure 事件中心连接字符串

**纠正措施**

1. 在 Datahub Swagger 中，转到合作伙伴 API。
2. 选择“获取” > “尝试” > “执行”  。

> [!NOTE]
> 你感兴趣的传感器合作伙伴的 ID。

3. 返回到合作伙伴 API，然后选择 "**获取/ \<ID> **"。
4. 指定步骤 3 中的合作伙伴 ID，然后选择“执行”。

   API 响应应包含事件中心连接字符串。

### <a name="device-appears-offline"></a>设备显示已脱机

**故障描述**：设备已安装，并且你已将 FarmBeats 关联到设备合作伙伴。 设备处于联机状态且正在发送遥测数据，但它们显示已脱机。

**纠正措施**：未为此设备配置报告间隔。 若要设置报告间隔，请联系你的设备制造商。 

### <a name="error-deleting-a-device"></a>删除设备时出错

删除设备时，可能会遇到下面某种常见错误情况：  

**消息**：“在传感器中引用设备：有一个或多个传感器与该设备关联。 请删除传感器，然后删除设备。”  

**含义**：设备与服务器场中部署的多个传感器关联。

**纠正措施**  

1. 通过加速器删除与设备关联的传感器。  
2. 如果要将传感器与其他设备关联，请要求设备合作伙伴也执行相同操作。  
3. 使用 `DELETE API` 调用删除设备，并将 force 参数设置为 true。  

**消息**：“在设备中将设备引用为 ParentDeviceId：有一个或多个设备作为子设备与该设备关联。 请删除它们，然后删除该设备。”  

**含义**：你的设备关联了其他设备。  

**纠正措施**

1. 删除与此特定设备关联的设备。  
2. 删除特定设备。  

    > [!NOTE]
    > 如果传感器与设备关联，则无法删除该设备。 要详细了解如何删除关联传感器，请参阅[从传感器合作伙伴获取传感器数据](get-sensor-data-from-sensor-partner.md)中的“删除传感器”部分。
    > 合作伙伴没有权限删除设备或传感器。 只有管理员才有删除权限。

## <a name="issues-with-jobs"></a>作业问题

### <a name="farmbeats-internal-error"></a>FarmBeats 内部错误

**消息**：“请参阅疑难解答指南，获取 FarmBeats 内部错误的详细信息。”

**纠正措施**：此问题可能是由于数据管道中的临时故障造成的。 请重新创建作业。 如果错误仍然存在，请联系我们并提供错误消息/日志。

## <a name="accelerator-troubleshooting"></a>加速器疑难解答

### <a name="access-control"></a>访问控制

**问题**：添加角色分配时收到错误。

**消息**：“找不到匹配的用户。”

**纠正措施**：检查要为其添加角色分配的电子邮件 ID。 电子邮件 ID 必须与在 Active Directory 中为该用户注册的 ID 完全匹配。 如果错误仍然存在，请联系我们并提供错误消息/日志。

### <a name="unable-to-log-in-to-accelerator"></a>无法登录到加速器

**消息**：“错误:你无权调用服务。 请与管理员联系以获取授权。”

**纠正措施**：请管理员授权你访问 FarmBeats 部署。 为此，可执行 RoleAssignment API 的 POST 操作或通过加速器中的“设置”窗格的访问控制进行操作。  

如果你已获得访问权限，但遇到此错误，请尝试刷新页面。 如果错误仍然存在，请联系我们并提供错误消息/日志。

![FarmBeats 项目](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>加速器问题  

**问题**：你收到了原因不明的加速器错误。

**消息**：“错误:发生未知错误。”

**纠正措施**：如果页面空闲时间过长，则将发生此错误。 刷新页面。 如果错误仍然存在，请联系我们并提供错误消息/日志。

**问题**：即使已升级 FarmBeatsDeployment，FarmBeats 加速器也不显示最新版本。

**纠正措施**：此错误是由于浏览器中服务工作进程持久性导致的。 请执行以下操作：

1. 关闭所有打开加速器的浏览器标签页，然后关闭浏览器窗口。
2. 启动浏览器的新实例，然后重新加载加速器 URI。 此操作将加载加速器的新版本。

## <a name="sentinel-imagery-related-issues"></a>Sentinel：图像相关问题

### <a name="wrong-username-or-password"></a>用户名或密码错误

**作业失败消息**：“需要完整的身份验证才能访问此资源。”

**纠正措施**：执行下列操作之一：

- 通过以下步骤使用正确的用户名/密码更新 FarmBeats，然后重试作业。

  **更新 Sentinel 用户名**

    1. 登录到 [Azure 门户](https://portal.azure.com)。
    2. 在“搜索”框中，搜索“FarmBeats 数据中心”资源组。
    3. 选择存储帐户存储***** >“容器” > “batch-prep-files” > “to_vm” > “config.ini”   
    4. 选择“编辑”
    5. 更新 sentinel_account 部分中的用户名

  **更新 Sentinel 密码**

    1. 登录到 [Azure 门户](https://portal.azure.com)。
    2. 在“搜索”框中，搜索“FarmBeats 数据中心”资源组。
    3. 选择 keyvault-*****
    4. 选择“设置”下的“访问策略”
    5. 选择“添加访问策略”
    6. 在“从模板配置”中使用“机密管理”，然后将你自己添加到主体
    7. 选择“添加”，然后选择“访问策略”页上的“保存”  
    8. 选择“设置”下的“机密” 
    9. 选择“Sentinel-password”
    10. 创建值的新版本并启用它。

- 重新运行失败的作业，或运行卫星索引作业 5 到 7 天，然后查看作业是否成功。

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel 中心：URL 错误或站点不可访问

**作业失败消息**：“糟糕，出错了。 你尝试访问的页面(暂时)不可用。”

**纠正措施**：

1. 在浏览器中打开 [Sentinel](https://scihub.copernicus.eu/dhus/)，查看网站是否可以访问。
2. 如果无法访问网站，请检查是否有任何防火墙、公司网络或其他阻止软件阻止访问网站，然后采取必要步骤以允许 Sentinel URL。 
3. 重新运行失败的作业，或运行卫星索引作业 5 到 7 天，然后查看作业是否成功。  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel 服务器：关闭进行维护

**作业失败消息**：“Copernicus Open Access Hub 即将回归! 很抱歉给你带来不便，我们目前正在进行一些维护。 稍后我们将重新上线!” 

**纠正措施**：

如果 Sentinel 服务器上正在执行任何维护活动，则可能会出现此问题。

1. 如果由于执行维护而导致任何作业或管道失败，则请在一段时间后重新提交作业。 

   要详细了解任何计划或未计划的 Sentinel 维护活动，请转到 [Copernicus Open Access Hub 新闻](https://scihub.copernicus.eu/news/)网站。  

2. 重新运行失败的作业，或运行卫星索引作业 5 到 7 天，然后查看作业是否成功。

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel：达到最大连接数

**作业失败消息**： "用户 ' ' 实现的两个并发流的最大数目 \<username> 。"

**含义**：如果作业由于已达到最大连接数而失败，则表示同一 Sentinel 帐户正在多个作业中使用。

**纠正措施**：请尝试执行以下操作之一：

* 请等待其他作业完成，然后重新运行失败的作业。
* 创建新的 Sentinel 帐户，然后在 FarmBeats 中更新 Sentinel 用户名和密码。

### <a name="sentinel-server-refused-connection"></a>Sentinel 服务器：连接遭到拒绝

**作业失败消息**：“服务器拒绝连接: http://172.30.175.69:8983/solr/dhus 。”

**纠正措施**：如果 Sentinel 服务器上正在执行任何维护活动，则可能会出现此问题。

1. 如果由于执行维护而导致任何作业或管道失败，则请在一段时间后重新提交作业。

   要详细了解任何计划或未计划的 Sentinel 维护活动，请转到 [Copernicus Open Access Hub 新闻](https://scihub.copernicus.eu/news/)网站。  

2. 重新运行失败的作业，或运行卫星索引作业 5 到 7 天，然后查看作业是否成功。

### <a name="soil-moisture-map-has-white-areas"></a>土壤湿度地图存在空白区域

**问题**：已生成土壤湿度地图，但该地图大部分区域为白色。

**纠正措施**：如果请求地图时生成的卫星索引的 NDVI 值小于 0.3，则可能会出现此问题。 有关详细信息，请访问 [Sentinel 的技术指南](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)。

1. 在不同的日期范围重新运行作业，并检查卫星索引中的 NDVI 值是否大于 0.3。

## <a name="collect-logs-manually"></a>手动收集日志

[安装并配置 Azure 存储资源管理器]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>在数据中心收集 Azure 数据工厂作业日志或应用服务日志

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在“搜索”框中，搜索“FarmBeats 数据中心”资源组。
3. 在资源组上，搜索“datahublogs\*”存储帐户， 例如 datahublogsmvxmq。  
4. 在“名称”列中，选择该存储帐户，查看“存储帐户”仪表板 。
5. 在“datahubblogs\*”窗格中，选择“在资源管理器中打开”，查看“打开 Azure 存储资源管理器”应用程序  。
6. 在左窗格中，选择“Blob 容器”，然后选择“job-logs”（针对 Azure 数据工厂日志）或“appinsights-logs”（针对应用服务日志）  。
7. 选择“下载”，并将日志下载到计算机上的本地文件夹。

    ![FarmBeats 项目](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>为加速器收集 Azure 数据工厂作业日志或应用服务日志

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在“搜索”框中，搜索“FarmBeats 加速器”资源组。
3. 在资源组上，搜索“storage\*”存储帐户， 例如 storagedop4k\*。
4. 在“名称”列中选择该存储帐户，查看“存储帐户”仪表板 。
5. 在“storage\*”窗格中，选择“在资源管理器中打开”，打开 Azure 存储资源管理器应用程序 。
6. 在左窗格中，选择“Blob 容器”，然后选择“job-logs”（针对 Azure 数据工厂日志）或“appinsights-logs”（针对应用服务日志）  。
7. 选择“下载”，并将日志下载到计算机上的本地文件夹。

## <a name="high-cpu-usage"></a>CPU 使用率较高

**错误**：你收到一个电子邮件警报，其中指出“高 CPU 使用率警报”。

**纠正措施**：

1. 请转到“FarmBeats 数据中心”资源组。
2. 选择“应用服务”。  
3. 转到按比例放大的[“应用服务定价”页面](https://azure.microsoft.com/pricing/details/app-service/windows/)，然后选择适当的定价层。

## <a name="weather-data-job-failures"></a>天气数据作业失败

**错误**：您运行作业以获取天气数据，但该作业失败

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>收集用于排查天气数据失败的日志

1. 在 Azure 门户中，请参阅 FarmBeats 资源组。
2. 单击属于资源组的数据工厂服务。 该服务将有标记 "sku： Datahub"

> [!NOTE]
> 若要查看资源组中服务的标记，请单击 "编辑列" 并向资源组视图添加 "标记"。

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="FarmBeats 项目":::

3. 在数据工厂的 "概述" 页上，单击 "**创作和监视**"。 此时会在浏览器中打开一个新选项卡。 单击**监视器**

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="FarmBeats 项目":::

4. 你将看到一个管道运行列表，其中包含天气作业执行的一部分。 单击要为其收集日志的作业
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="FarmBeats 项目":::

5. 在 "管道概述" 页上，你将看到活动运行的列表。 记下要为其收集日志的活动的运行 Id
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="FarmBeats 项目":::

6. 返回到 Azure 门户中的 FarmBeats 资源组，并单击名为**datahublogs**的存储帐户
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="FarmBeats 项目":::

7. 单击 "**容器**  ->  **adfjobs**"。 在搜索框中，输入前面步骤5中记下的作业运行 ID。
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="FarmBeats 项目":::

8. 搜索结果将包含包含与作业相关的日志的文件夹。 下载日志并将其发送到， farmbeatssupport@microsoft.com 以便在调试问题时获得帮助。
