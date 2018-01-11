---
title: "使用 .NET 配置 Azure 媒体服务遥测 | Microsoft 文档"
description: "本文说明了如何通过 .NET SDK 使用 Azure 媒体服务遥测。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: f8f55e37-0714-49ea-bf4a-e6c1319bec44
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 1f8e22dc5e277407860b7ed31409caed15be59cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="configuring-azure-media-services-telemetry-with-net"></a>使用 .NET 配置 Azure 媒体服务遥测

本文介绍了使用 .NET SDK 配置 Azure 媒体服务 (AMS) 遥测时可采取的常规步骤。 

>[!NOTE]
>有关 AMS 遥测定义和使用方法的详细介绍，请参阅[概述](media-services-telemetry-overview.md)一文。

可通过以下方式之一使用遥测数据：

- 直接从 Azure 表存储中读取数据（例如使用存储 SDK）。 有关遥测存储表的说明，请参阅[这篇文章](https://msdn.microsoft.com/library/mt742089.aspx)中的“使用遥测信息”。

或

- 使用媒体服务 .NET SDK 中支持的内容来读取存储数据。 本文说明了如何为指定的 AMS 帐户启用遥测，以及如何使用 Azure 媒体服务 .NET SDK 查询指标。  

## <a name="configuring-telemetry-for-a-media-services-account"></a>为媒体服务帐户配置遥测

启用遥测需要执行以下步骤：

- 获取已附加到媒体服务帐户的存储帐户的凭据。 
- 创建一个通知终结点，将其 **EndPointType** 设置为 **AzureTable**，并使 endPontAddress 指向存储表。

        INotificationEndPoint notificationEndPoint = 
                      _context.NotificationEndPoints.Create("monitoring", 
                      NotificationEndPointType.AzureTable,
                      "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");

- 为要监视的服务创建监视配置设置。 最多允许一个监视配置设置。 
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });

## <a name="consuming-telemetry-information"></a>使用遥测信息

有关使用遥测信息的信息，请参阅[这篇文章](media-services-telemetry-overview.md)。

## <a name="create-and-configure-a-visual-studio-project"></a>创建和配置 Visual Studio 项目

1. 设置开发环境，并根据[使用 .NET 进行媒体服务开发](media-services-dotnet-how-to-use.md)中所述，在 app.config 文件中填充连接信息。 

2. 将以下元素添加到 app.config 文件中定义的 **appSettings**：

    <add key="StorageAccountName" value="storage_name" />
 
## <a name="example"></a>示例  
    
以下示例说明了如何为指定的 AMS 帐户启用遥测，以及如何使用 Azure 媒体服务 .NET SDK 查询指标。  

```
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace AMSMetrics
{
    class Program
    {
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly string _mediaServicesStorageAccountName =
            ConfigurationManager.AppSettings["StorageAccountName"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static IStreamingEndpoint _streamingEndpoint = null;
        private static IChannel _channel = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            _streamingEndpoint = _context.StreamingEndpoints.FirstOrDefault();
            _channel = _context.Channels.FirstOrDefault();

            var monitoringConfigurations = _context.MonitoringConfigurations;
            IMonitoringConfiguration monitoringConfiguration = null;

            // No more than one monitoring configuration settings is allowed.
            if (monitoringConfigurations.ToArray().Length != 0)
            {
                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
            }
            else
            {
                INotificationEndPoint notificationEndPoint =
                          _context.NotificationEndPoints.Create("monitoring",
                          NotificationEndPointType.AzureTable, GetTableEndPoint());

                monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
                    new List<ComponentMonitoringSetting>()
                    {
                    new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                    new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)

                    });
            }

            //Print metrics for a Streaming Endpoint.
            PrintStreamingEndpointMetrics();

            Console.ReadLine();
        }

        private static string GetTableEndPoint()
        {
            return "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/";
        }

        private static void PrintStreamingEndpointMetrics()
        {
            Console.WriteLine(string.Format("Telemetry for streaming endpoint '{0}'", _streamingEndpoint.Name));

            DateTime timerangeEnd = DateTime.UtcNow;
            DateTime timerangeStart = DateTime.UtcNow.AddHours(-5);

            // Get some streaming endpoint metrics.
            var telemetry = _streamingEndpoint.GetTelemetry();

            var res = telemetry.GetStreamingEndpointRequestLogs(timerangeStart, timerangeEnd);

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
            if (_channel == null)
            {
                Console.WriteLine("There are no channels in this AMS account");
                return;
            }

            Console.WriteLine(string.Format("Telemetry for channel '{0}'", _channel.Name));

            DateTime timerangeEnd = DateTime.UtcNow;
            DateTime timerangeStart = DateTime.UtcNow.AddHours(-5);

            // Get some channel metrics.
            var telemetry = _channel.GetTelemetry();

            var channelMetrics = telemetry.GetChannelHeartbeats(timerangeStart, timerangeEnd);

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
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
