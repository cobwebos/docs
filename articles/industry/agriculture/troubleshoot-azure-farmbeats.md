---
title: 排查 Azure FarmBeats 问题
description: 本文介绍如何对 Azure 服务器场节拍进行故障排除。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 7a31eece6629558b14b614853addce59642e698b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422727"
---
# <a name="troubleshoot"></a>疑难解答

本文提供了常见 Azure 农场节拍问题的解决方案。

如需其他帮助，请通过farmbeatssupport@microsoft.com联系我们。 确保在电子邮件中包含**部署器.log**文件。

要下载**Deployer.log**文件，请执行以下操作：

1. 登录到 Azure**门户**并选择订阅和 Azure AD 租户。
2. 从 Azure 门户的顶部导航栏启动 Cloud Shell。
3. 选择**Bash**作为首选云外壳体验。
4. 选择突出显示的图标，然后在下拉列表中选择 **"下载**"。

    ![项目农场节拍](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. 在下一个窗格中，输入**Deployer.log**文件的路径。 例如，输入**服务器场节拍部署器.log**。

## <a name="sensor-telemetry"></a>传感器遥测

### <a name="cant-view-telemetry-data"></a>无法查看遥测数据

**症状**：设备或传感器已部署，并且您已与设备合作伙伴链接了 FarmBeats，但无法获取或查看 FarmBeats 上的遥测数据。

**纠正措施**：

1. 转到服务器场节拍 Datahub 资源组。   

2. 选择**事件中心**（数据馈送事件Hub命名空间），然后检查传入消息的数量。

3. 执行下列任一操作：   
   - 如果没有*传入的消息*，请与您的设备合作伙伴联系。  
   - 如果有*传入消息*，请与farmbeatssupport@microsoft.com. 附加 Datahub 和加速器日志和捕获的遥测数据。

要了解如何下载日志，请转到["手动收集日志"](#collect-logs-manually)部分。  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>从传感器引入历史/流数据后无法查看遥测数据

**症状**：设备或传感器已部署，您已创建 FarmBeats 上的设备/传感器，并将遥测数据引入到 EventHub，但无法获取或查看 FarmBeats 上的遥测数据。

**纠正措施**：

1. 确保合作伙伴注册工作正确 - 您可以通过访问数据库摇曳器、导航到 /合作伙伴 API、执行获取并检查合作伙伴是否已注册来检查此情况。 如果没有，请按照[此处的步骤](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)添加合作伙伴。

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

**纠正措施**：

1. 在 Datahub Swagger 中，转到合作伙伴 API。
2. 选择 **"试用** > **它** > **执行**"。

> [!NOTE]
> 您感兴趣的传感器合作伙伴的合作伙伴 ID。

3. 返回合作伙伴 API，然后选择**获取/ID\<>**。
4. 从步骤 3 中指定合作伙伴 ID，然后选择 **"执行**"。

   API 响应应具有事件中心连接字符串。

### <a name="device-appears-offline"></a>设备显示为脱机

**症状**：设备已安装，您已与设备合作伙伴链接了 FarmBeats。 设备处于联机状态并发送遥测数据，但它们显示为脱机。

**纠正措施**：未为此设备配置报告间隔。 要设置报告间隔，请与设备制造商联系。 

### <a name="error-deleting-a-device"></a>删除设备错误

删除设备时，可能会遇到以下常见错误方案之一：  

**消息**："传感器中引用了设备：有一个或多个传感器与设备相关联。 删除传感器，然后删除设备。  

**含义**：设备与部署在服务器场中的多个传感器相关联。   

**纠正措施**：  

1. 通过加速器删除与设备关联的传感器。  
2. 如果要将传感器与其他设备关联，请让设备合作伙伴也这样做。  
3. 使用`DELETE API`调用删除设备，并将强制参数设置为*true*。  

**消息**："设备在设备中被引用为父设备Id：有一个或多个设备作为子设备与此设备关联。 删除它们，然后删除此设备。  

**含义**：您的设备具有与其关联的其他设备。  

**纠正措施**

1. 删除与此特定设备关联的设备。  
2. 删除特定设备。  

    > [!NOTE]
    > 如果传感器与其关联，则无法删除设备。 有关如何删除相关传感器的详细信息，请参阅[从传感器合作伙伴获取传感器数据的](get-sensor-data-from-sensor-partner.md)**"删除传感器"** 部分。

    > 合作伙伴无法删除设备或传感器。 只有管理员才能执行此操作。


## <a name="issues-with-jobs"></a>工作问题

### <a name="farmbeats-internal-error"></a>服务器场内部错误

**消息**："FarmBeats 内部错误，请参阅故障排除指南，了解更多详细信息"。

**纠正措施**：此问题可能是由于数据管道中的临时故障造成的。 再次创建作业。 如果错误仍然存在，请在 FarmBeats 论坛的帖子中添加错误消息，或联系FarmBeatsSupport@microsoft.com。

## <a name="accelerator-troubleshooting"></a>加速器故障排除

### <a name="access-control"></a>访问控制

**问题**：添加角色分配时收到错误。

**消息**："未找到匹配的用户。

**纠正措施**：检查您尝试为其添加角色分配的电子邮件 ID。 电子邮件 ID 必须与该 ID 完全匹配，该 ID 是在活动目录中为该用户注册的。 如果错误仍然存在，请在 FarmBeats 论坛的帖子中添加错误消息，或联系FarmBeatsSupport@microsoft.com。

### <a name="unable-to-log-in-to-accelerator"></a>无法登录到加速器

**消息**："错误：您无权调用服务。 请与管理员联系以寻求授权。

**纠正措施**：要求管理员授权您访问服务器场节拍部署。 这可以通过执行角色分配 API 的 POST 或通过加速器中的 **"设置"** 窗格中的"访问控制"来实现。  

如果您已被授予访问权限并面临此错误，请尝试刷新页面。 如果错误仍然存在，请在 FarmBeats 论坛的帖子中添加错误消息，或联系FarmBeatsSupport@microsoft.com。

![项目农场节拍](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>加速器问题  

**问题**：您收到未确定原因的加速器错误。

**消息**："错误：发生未知错误。

**更正操作**：如果页面空闲时间过长，则会发生此错误。 刷新页面。  

如果错误仍然存在，请在 FarmBeats 论坛的帖子中添加错误消息，或联系FarmBeatsSupport@microsoft.com。

**问题**： FarmBeats 加速器不会显示最新版本，即使在升级了服务器场Beats部署之后也是如此。

**更正操作**：由于浏览器中的服务辅助角色持久性，发生此错误。 请执行以下操作：

1. 关闭打开加速器的所有浏览器选项卡，然后关闭浏览器窗口。
2. 启动浏览器的新实例，然后重新加载加速器 URI。 此操作加载新版本的加速器。

## <a name="sentinel-imagery-related-issues"></a>哨兵：与影像有关的问题

### <a name="wrong-username-or-password"></a>错误的用户名或密码

**作业失败消息**："访问此资源需要完全身份验证"。

**纠正措施**：

执行下列操作之一：

- 重新运行安装程序，以便使用正确的用户名和密码升级 Datahub。
- 重新运行失败的作业，或运行 5 到 7 天的日期范围的卫星索引作业，然后检查作业是否成功。

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>哨兵中心：无法访问错误的 URL 或站点 

**工作失败消息**："哎呀，出事了。 您尝试访问的页面（暂时）不可用。 

**纠正措施**：
1. 在浏览器中打开[哨兵](https://scihub.copernicus.eu/dhus/)以查看网站是否可以访问。 
2. 如果无法访问网站，请检查是否有任何防火墙、公司网络或其他阻止软件阻止访问网站，然后采取必要步骤允许 Sentinel URL。 
3. 重新运行失败的作业，或运行 5 到 7 天的日期范围的卫星索引作业，然后检查作业是否成功。  

### <a name="sentinel-server-down-for-maintenance"></a>哨兵服务器：停机进行维护

**工作失败消息**："哥白尼开放访问中心将很快回来！ 很抱歉给您带来不便，我们目前正在进行一些维护。 我们很快就会回到网上！ 

**纠正措施**：

如果在 Sentinel 服务器上执行任何维护活动，则可能会出现此问题。

1. 如果由于执行维护而导致任何作业或管道失败，则在一段时间后重新提交作业。 

   有关任何计划外哨兵维护活动的信息，请访问[哥白尼开放访问中心新闻网站](https://scihub.copernicus.eu/news/)。  

2. 重新运行失败的作业，或运行 5 到 7 天的日期范围的卫星索引作业，然后检查作业是否成功。

### <a name="sentinel-maximum-number-of-connections-reached"></a>哨兵：达到的最大连接数

**作业失败消息**："用户"用户名>"\<实现的最大两个并发流数。

**含义**：如果作业由于已达到最大连接数而失败，则同一 Sentinel 帐户正在其他软件部署中使用。

**纠正措施**：请尝试以下任一操作：

* 创建新的 Sentinel 帐户，然后重新运行安装程序，使用新的 Sentinel 用户名和密码升级 Datahub。  
* 重新运行失败的作业或运行 5 到 7 天的日期范围的卫星索引作业，然后检查作业是否成功。

### <a name="sentinel-server-refused-connection"></a>哨兵服务器：拒绝连接 

**作业失败消息**："服务器拒绝连接："。 http://172.30.175.69:8983/solr/dhus 

**纠正措施**：如果在 Sentinel 服务器上执行任何维护活动，则可能会出现此问题。 
1. 如果由于执行维护而导致任何作业或管道失败，则在一段时间后重新提交作业。 

   有关任何计划外哨兵维护活动的信息，请访问[哥白尼开放访问中心新闻网站](https://scihub.copernicus.eu/news/)。  

2. 重新运行失败的作业，或运行 5 到 7 天的日期范围的卫星索引作业，然后检查作业是否成功。

### <a name="soil-moisture-map-has-white-areas"></a>土壤水分图有白色区域 

**问题**： 土壤水分地图生成，但地图大多是白色区域。

**纠正措施**：如果请求地图时生成的卫星索引的 NDVI 值小于 0.3，则可能会出现此问题。 欲了解更多信息，请访问[《哨兵》的技术指南](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)。
1. 重新运行不同日期范围的作业，并检查卫星索引中的 NDVI 值是否大于 0.3

## <a name="collect-logs-manually"></a>手动收集日志

[安装和部署 Azure 存储资源管理器]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>在 Datahub 中收集 Azure 数据工厂作业日志

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在 **"搜索"** 框中，搜索服务器场节拍 Datahub 资源组。

    > [!NOTE]
    > 选择您在服务器场节拍安装期间指定的 Datahub 资源组。

3. 在 **"资源组**"仪表板上，搜索*\*datahublogs*存储帐户。 例如，搜索**数据博客mvxmq**。  
4. 在"**名称**"列中，选择存储帐户以查看**存储帐户**仪表板。
5. 在**数据库\*博客**窗格中，选择 **"在资源管理器中打开**"以查看**打开的 Azure 存储资源管理器**应用程序。
6. 在左侧窗格中，选择**Blob 容器**，然后选择**作业日志**。
7. 在**作业日志**窗格中，选择 **"下载**"。
8. 将日志下载到计算机上的本地文件夹。
9. 将下载的 .zip 文件farmbeatssupport@microsoft.com通过电子邮件发送到 。

    ![项目农场节拍](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>在加速器中收集 Azure 数据工厂作业日志

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在 **"搜索"** 框中，搜索服务器场节拍加速器资源组。

    > [!NOTE]
    > 选择在服务器场节拍安装期间指定的加速器资源组。

3. 在 **"资源组**"仪表板上，搜索*存储\** 存储帐户。 例如，搜索**存储多普4k\***。
4. 在 **"名称"** 列中选择存储帐户以查看**存储帐户**仪表板。
5. 在**存储\*** 窗格中，选择 **"在资源管理器中打开**"以打开 Azure 存储资源管理器应用程序。
6. 在左侧窗格中，选择**Blob 容器**，然后选择**作业日志**。
7. 在**作业日志**窗格中，选择 **"下载**"。
8. 将日志下载到计算机上的本地文件夹。
9. 将下载的 .zip 文件farmbeatssupport@microsoft.com通过电子邮件发送到 。


### <a name="collect-datahub-app-service-logs"></a>收集 Datahub 应用服务日志

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在 **"搜索"** 框中，搜索服务器场节拍 Datahub 资源组。

    > [!NOTE]
    > 选择您在服务器场节拍安装期间指定的 Datahub 资源组。

3. 在资源组中，搜索*datahublogs\** 存储帐户。 例如，搜索**datahublogsmvxmq\***。
4. 在 **"名称"** 列中选择存储帐户以查看**存储帐户**仪表板。
5. 在**数据库博客\*** 窗格中，选择 **"在资源管理器中打开**"以打开 Azure 存储资源管理器应用程序。
6. 在左侧窗格中，选择**Blob 容器**，然后选择**应用见解日志**。
7. 在**应用见解日志**窗格中，选择 **"下载**"。
8. 将日志下载到计算机上的本地文件夹。
9. 将下载的 .zip 文件farmbeatssupport@microsoft.com通过电子邮件发送到 。

### <a name="collect-accelerator-app-service-logs"></a>收集加速器应用服务日志

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在 **"搜索"** 框中，搜索服务器场节拍加速器资源组。

    > [!NOTE]
    > 选择服务器场节拍安装期间提供的服务器场节拍加速器资源组。

3. 在资源组中，搜索*存储\** 存储帐户。 例如，搜索**存储多普4k\***。
4. 在 **"名称"** 列中选择存储帐户以查看**存储帐户**仪表板。
5. 在**存储\*** 窗格中，选择 **"在资源管理器中打开**"以打开 Azure 存储资源管理器应用程序。
6. 在左侧窗格中，选择**Blob 容器**，然后选择**应用见解日志**。
7. 在**应用见解日志**窗格中，选择 **"下载**"。
8. 将日志下载到计算机上的本地文件夹。
9. 将下载的文件夹通过电子邮件发送到farmbeatssupport@microsoft.com。

## <a name="known-issues"></a>已知问题

## <a name="batch-related-issues"></a>批次相关问题

**错误消息**："已达到指定订阅的批处理帐户的区域帐户配额。

**纠正措施**：增加配额，或删除未使用的批处理帐户并重新运行部署。

### <a name="azure-active-directory-azure-ad-related-issues"></a>Azure 活动目录 （Azure AD） 相关问题

**错误消息**："无法将所需的设置更新到 Azure AD 应用 d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0：无法完成操作的权限不足。 确保为 Azure AD 应用正确配置了上述设置。

**含义**：Azure AD 应用注册配置未正确完成。  

**纠正措施**：要求 IT 管理员（具有租户读取访问权限的人员）使用我们的[脚本](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts)创建 Azure AD 应用注册。 此脚本还会自动处理配置步骤。

**错误消息**："无法在此租户中创建新的活动目录应用程序 '\<应用程序名称\>'：属性标识符 URI 的值相同的另一个对象已存在。

**含义**：已存在同名的 Azure AD 应用注册。

**纠正措施**：删除现有的 Azure AD 应用注册，或将其重复使用以进行安装。 如果要重用现有的 Azure AD 应用注册，请将应用程序 ID 和客户端密钥传递给安装程序并重新部署。

## <a name="issues-with-the-inputjson-file"></a>input.json 文件的问题

**错误**：从*input.json*文件中读取输入时出错。

**纠正措施**：此问题通常是因为向安装程序指定正确的*input.json*文件路径或名称时出错。 进行适当的更正，并重试部署。

**错误**：*在 input.json*文件中存在错误分析值。

**纠正措施**：此问题主要由于*input.json*文件中的值不正确而出现。 进行适当的更正，并重试部署。

## <a name="high-cpu-usage"></a>CPU 使用率较高

**错误**： 您收到一个电子邮件警报，其中提到**高 CPU 使用率警报**。 

**纠正措施**： 
1. 转到服务器场节拍 Datahub 资源组。
2. 选择**应用服务**。  
3. 转到"向上扩展[应用服务定价"页面](https://azure.microsoft.com/pricing/details/app-service/windows/)，然后选择适当的定价层。

## <a name="next-steps"></a>后续步骤

如果您仍然面临 FarmBeats 问题，请联系我们[的支持论坛](https://aka.ms/farmbeatssupport)。
