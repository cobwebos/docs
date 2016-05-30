<properties
 pageTitle="远程监视预配置解决方案演练 | Azure"
 description="介绍 Azure IoT 预配置解决方案远程监视及其体系结构。"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.date="03/02/2016"
 wacn.date="05/17/2016"/>

# 远程监视预配置解决方案演练

## 介绍

IoT 套件远程监视预配置解决方案是一个用于商业应用场景的基本端到端监视解决方案，可以在远程位置运行多台计算机。该解决方案结合主要的 Azure IoT 套件服务，提供商业应用场景的一般实现，同时也是计划实施此类型的 IoT 解决方案以符合各自特定业务需求的客户的起点。

## 逻辑体系结构

下图概述该预配置解决方案的逻辑组件：

![](./media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### 模拟设备

在该预配置解决方案中，模拟设备表示冷却设备（例如建筑物空调或设施空气处理单位）。每个模拟设备会将下列遥测消息发送到 IoT 中心：


| 消息 | 说明 |
|----------|-------------|
| 启动 | 当设备启动时，它会发送**设备信息**消息，其中包含自身的相关信息，例如设备 ID、设备元数据、设备支持的命令列表，以及设备的当前配置。 |


模拟设备会发送下列设备属性作为元数据：

| 属性 | 目的 |
|------------------------|--------- |
| 设备 ID | 在解决方案中创建设备时所提供或分配的 ID。 |
| 制造商 | 设备制造商 |
| 型号 | 设备的型号 |
| 序列号 | 设备的序列号 |
| 固件 | 设备上的当前固件版本 |
| 平台 | 设备的平台体系结构 |
| 处理器 | 运行设备的处理器 |
| 已安装的 RAM | 在设备上安装的 RAM 量 |
| 中心已启用状态 | 设备的 IoT 中心状态属性 |
| 创建时间 | 在解决方案中创建设备的时间 |
| 更新时间 | 设备上次更新属性的时间 |
| 纬度 | 设备的纬度位置 |
| 经度 | 设备的经度位置 |

模拟器会以示例值在模拟设备中植入这些属性。模拟器每次初始化模拟设备时，设备会将预定义的元数据发布到 IoT 中心。请注意，这会覆盖设备门户中所做的任何元数据更新。


模拟设备可以处理从 IoT 中心发送的下列命令：

| 命令 | 说明 |
|------------------------|-----------------------------------------------------|
| PingDevice | 向设备发送 _ping_ 以检查其是否处于活动状态 |
| StartTelemetry | 使设备开始发送遥测 |
| StopTelemetry | 使设备停止发送遥测 |
| ChangeSetPointTemp | 更改设置点值（将围绕其生成随机数据） |
| DiagnosticTelemetry | 触发设备模拟器以发送其他遥测值 (externalTemp) |
| ChangeDeviceState | 更改设备的扩展状态属性，并从设备发送设备信息消息 |


设备命令确认通过 IoT 中心提供。


### Azure 流分析作业


**作业 1：设备信息**会筛选来自传入消息流的设备信息消息，并将它们发送到事件中心终结点。设备会在启动时发送设备信息消息，并且响应 **SendDeviceInfo** 命令。此作业使用下列查询定义：

	
	SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'


**作业 2：规则**会针对每个设备的阈值评估传入温度和湿度遥测值。阈值在解决方案所包含的规则编辑器中设置。每个设备/值对按照时间戳存储在 blob 中，会读取到流分析作为**参考数据**。该作业会针对设备的设置阈值比较任何非空值。如果超过“>”条件，该作业将输出**警报**事件，表示已超过阈值，并且提供设备、值和时间戳值。此作业使用下列查询定义：
	
	WITH AlarmsData AS 
	(
	SELECT
	     Stream.DeviceID,
	     'Temperature' as ReadingType,
	     Stream.Temperature as Reading,
	     Ref.Temperature as Threshold,
	     Ref.TemperatureRuleOutput as RuleOutput,
	     Stream.EventEnqueuedUtcTime AS [Time]
	FROM IoTTelemetryStream Stream
	JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
	WHERE
	     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature
	
	UNION ALL
	
	SELECT
	     Stream.DeviceID,
	     'Humidity' as ReadingType,
	     Stream.Humidity as Reading,
	     Ref.Humidity as Threshold,
	     Ref.HumidityRuleOutput as RuleOutput,
	     Stream.EventEnqueuedUtcTime AS [Time]
	FROM IoTTelemetryStream Stream
	JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
	WHERE
	     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
	)
	
	SELECT *
	INTO DeviceRulesMonitoring
	FROM AlarmsData
	
	SELECT *
	INTO DeviceRulesHub
	FROM AlarmsData


**作业 3：遥测**会通过两种方法来操作传入设备遥测流。第一种方法会将设备的所有遥测消息都发送到持久 blob 存储。第二种方法会通过五分钟滑动窗口计算平均、最小和最大湿度值。这些数据也会发送到 blob 存储。此作业使用下列查询定义：

	
	WITH 
	    [StreamData]
	AS (
	    SELECT
	        *
	    FROM 
	      [IoTHubStream] 
	    WHERE
	        [ObjectType] IS NULL -- Filter out device info and command responses
	) 
	
	SELECT
	    *
	INTO
	    [Telemetry]
	FROM
	    [StreamData]
	
	SELECT
	    DeviceId,
	    AVG (Humidity) AS [AverageHumidity], 
	    MIN(Humidity) AS [MinimumHumidity], 
	    MAX(Humidity) AS [MaxHumidity], 
	    5.0 AS TimeframeMinutes 
	INTO
	    [TelemetrySummary]
	FROM
	    [StreamData]
	WHERE
	    [Humidity] IS NOT NULL
	GROUP BY
	    DeviceId, 
	    SlidingWindow (mi, 5)


### 事件处理器

**事件处理器**会处理设备信息消息和命令响应。它使用：

- 具有当前设备信息的设备信息消息以更新设备注册表（存储在 DocumentDB 数据库中）。
- 命令响应消息以更新设备命令历史记录（存储在 DocumentDB 数据库中）。

## 让我们开始

本节将逐步解说解决方案的组件、说明预期的用例，并提供示例。

### 远程监视仪表板
Web 应用程序中的此页面会使用 PowerBI javascript 控件（请参阅 [PowerBI-visuals repo（PowerBI 可视化效果存储库）](https://www.github.com/Microsoft/PowerBI-visuals)）以可视化方式呈现 blob 存储中流分析作业的输出数据。


### 设备管理门户

此 Web 应用可让你：

- 预配新的设备，这样会设置唯一设备 ID 并生成身份验证密钥。
- 管理设备属性，其中包括查看现有属性和使用新属性进行更新。
- 将命令发送到设备。
- 查看设备的命令历史记录。

### 观察云解决方案的行为
你可以查看已预配的资源，方法是转到 [Azure 门户](https://portal.azure.cn)并导航到具有你指定的解决方案名称的资源组。

![](./media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

第一次运行示例时，有四个预配置的模拟设备：

![](./media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

你可以使用设备管理门户添加新的模拟设备：

![](./media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

一开始，设备管理门户中新设备的状态是“挂起”：

![](./media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

应用完成模拟设备部署后，你会看到设备的状态在设备管理门户中更改为“正在运行”，如下列屏幕截图所示。**DeviceInfo** 流分析作业会将设备的设备状态信息发送到设备管理门户。

![](./media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

可以使用解决方案门户将 **ChangeSetPointTemp** 等命令发送给设备：

![](./media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

当设备报告它已成功执行命令时，状态会更改为“成功”：

![](./media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

可以使用解决方案门户搜索具有特定特征（例如型号）的设备：

![](./media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

可以禁用设备，并且在已禁用之后删除：

![](./media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)


## 后续步骤

下列 TechNet 博客文章提供有关远程监视预配置解决方案的其他详细信息：

- [IoT Suite - Under The Hood - Remote Monitoring（IoT 套件 - 幕后 - 远程监视）](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices（IoT 套件 - 远程监视 - 添加实时与模拟设备）](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

<!---HONumber=Mooncake_0523_2016-->