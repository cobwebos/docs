<properties pageTitle="使用 .NET 进行 Azure 媒体服务遥测 | Microsoft Azure" 
	description="本文说明如何使用 Azure 媒体服务遥测。" 
	services="" 
	documentationCenter=""
	authors="juliako" />

# 使用 .NET 进行 Azure 媒体服务遥测

## 概述

媒体服务遥测/监视允许媒体服务客户访问其服务的指标数据。当前版本支持“Channel”和“StreamingEndpoint”实体的遥测数据。你可以按组件级的粒度配置遥测。有两种详细级别：“正常”和“详细”。当前版本仅支持“正常”。

遥测数据将写入客户提供的 Azure 存储帐户（必须将一个存储帐户附加到媒体服务帐户）。遥测数据将写入指定存储帐户中的 Azure 存储表。遥测系统将为每个新日期（基于 00:00 UTC）单独创建一个表。例如“TelemetryMetrics20160321”，其中的“20160321”是表的创建日期。每个日期都有单独的表。

遥测系统不提供数据保留，也不会自动删除旧记录。为此，你需要管理和删除旧记录。由于每个日期都有单独的表，因此你可以轻松删除旧记录。只需删除旧表即可。

本主题说明如何为指定的 AMS 服务启用遥测，以及如何使用 .NET 查询指标。

## 为媒体服务帐户配置遥测

启用遥测需要执行以下步骤：

- 获取已附加到媒体服务帐户的存储帐户的凭据。
- 创建一个通知终结点，其 **EndPointType** 设置为 **AzureTable**，endPontAddress 指向存储表。

	    INotificationEndPoint notificationEndPoint = 
	                  _context.NotificationEndPoints.Create("monitoring", 
	                  NotificationEndPointType.AzureTable,
	                  "https://" + _mediaServicesStorageAccountName + ".table.core.chinacloudapi.cn/");

- 为要监视的服务创建监视配置设置。最多允许一个监视配置设置。
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });


## StreamingEndpoint 日志

###可用度量值

可以查询以下 StreamingEndPoint 指标。

- **PartitionKey** 获取记录的分区键。
- **RowKey** 获取记录的行键。
- **AccountId** 获取媒体服务帐户 ID。
- **AccountId** 获取媒体服务流式处理终结点 ID。
- **ObservedTime** 获取指标的观测时间。
- **HostName** 获取流式处理终结点主机名。
- **StatusCode** 获取状态代码。
- **ResultCode** 获取结果代码。
- **RequestCount** 获取请求计数。
- **BytesSent** 获取发送的字节数。
- **ServerLatency** 获取服务器延迟。
- **EndToEndLatency** 获取端到端请求时间。

###流式处理终结点查询结果示例

![流式处理终结点查询](media/media-services-telemetry/media-services-telemetry01.png)


## 实时通道检测信号

###可用度量值

可以查询以下实时通道指标。

- **PartitionKey** 获取记录的分区键。
- **RowKey** 获取记录的行键。
- **AccountId** 获取媒体服务帐户 ID。
- **ChannelId** 获取媒体服务通道 ID。
- **ObservedTime** 获取指标的观测时间。
- **CustomAttributes** 获取自定义属性。
- **TrackType** 获取跟踪类型。
- **TrackName** 获取跟踪名称。
- **Bitrate** 获取比特率。
- **IncomingBitrate** 获取传入比特率。
- **OverlapCount** 获取重叠计数。
- **DiscontinuityCount** 获取中断计数。
- **LastTimestamp** 获取最后一个时间戳。
 
###实时通道查询结果示例

![流式处理终结点查询](media/media-services-telemetry/media-services-telemetry01.png)

## StreamingEndpoint 指标示例
		
	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace AMSMetrics
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	        private static readonly string _mediaServicesAccountID =
	            ConfigurationManager.AppSettings["MediaServicesAccountID"];
	        private static readonly string _streamingEndpointID =
	            ConfigurationManager.AppSettings["StreamingEndpointID"];
	        private static readonly string _mediaServicesStorageAccountName =
	            ConfigurationManager.AppSettings["StorageAccountName"];
	        private static readonly string _mediaServicesStorageAccountKey =
	            ConfigurationManager.AppSettings["StorageAccountKey"];
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            INotificationEndPoint notificationEndPoint = 
	                          _context.NotificationEndPoints.Create("monitoring", NotificationEndPointType.AzureTable, GetTableEndPoint());
	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
	                    new List<ComponentMonitoringSetting>()
	                    {
	                        new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
	                    });
	            }
	
	            //Print metrics for a Streaming Endpoint.
	            PrintStreamingEndpointMetrics();
	
	            Console.ReadLine();
	        }
	
	        private static string GetTableEndPoint()
	        {
	            return "https://" + _mediaServicesStorageAccountName + ".table.core.chinacloudapi.cn/";
	        }
	
	        private static void PrintStreamingEndpointMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some streaming endpoint metrics.
	            var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
	                    GetTableEndPoint(),
	                    _mediaServicesStorageAccountKey,
	                    _mediaServicesAccountID,
	                    _streamingEndpointID,
	                    start,
	                    end);
	
	
	            Console.Title = "Streaming endpoint metrics:";
	
	            foreach (var log in res)
	            {
	                Console.WriteLine("AccountId: {0}", log.AccountId);
	                Console.WriteLine("BytesSent: {0}", log.BytesSent);
	                Console.WriteLine("EndToEndLatency: {0}", log.EndToEndLatency);
	                Console.WriteLine("HostName: {0}", log.HostName);
	                Console.WriteLine("ObservedTime: {0}", log.ObservedTime);
	                Console.WriteLine("PartitionKey: {0}", log.PartitionKey);
	                Console.WriteLine("RequestCount: {0}", log.RequestCount);
	                Console.WriteLine("ResultCode: {0}", log.ResultCode);
	                Console.WriteLine("RowKey: {0}", log.RowKey);
	                Console.WriteLine("ServerLatency: {0}", log.ServerLatency);
	                Console.WriteLine("StatusCode: {0}", log.StatusCode);
	                Console.WriteLine("StreamingEndpointId: {0}", log.StreamingEndpointId);
	                Console.WriteLine();
	            }
	
	            Console.WriteLine();
	        }

	        private static void PrintChannelMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some channel metrics.
	            var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
	                GetTableEndPoint(),
	                _mediaServicesStorageAccountKey,
	                _mediaServicesAccountName,
	                _context.Channels.FirstOrDefault().Id,
	                start,
	                end);
	
	            // Print the channel metrics.
	            Console.WriteLine("Channel metrics:");
	
	            foreach (var channelHeartbeat in channelMetrics.OrderBy(x => x.ObservedTime))
	            {
	                Console.WriteLine(
	                    "    Observed time: {0}, Last timestamp: {1}, Incoming bitrate: {2}",
	                    channelHeartbeat.ObservedTime,
	                    channelHeartbeat.LastTimestamp,
	                    channelHeartbeat.IncomingBitrate);
	            }
	
	            Console.WriteLine();
	        }
	    }
	}

<!---HONumber=Mooncake_0808_2016-->