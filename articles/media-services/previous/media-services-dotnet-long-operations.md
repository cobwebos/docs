---
title: 轮询长时运行的操作 | Microsoft Docs
description: 本主题说明如何轮询长时间运行的操作。
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: fa456a2d0bb427af80d67a0b971fe8bd41bb7868
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>使用 Azure 媒体服务传送实时流

## <a name="overview"></a>概述

Microsoft Azure 媒体服务提供了相应的 API 用来请求媒体服务启动操作（例如创建、启动、停止或删除频道）。 这些操作是长时运行的。

媒体服务 .NET SDK 提供了用来发送请求并等待操作完成的 API（在内部，这些 API 以特定的时间间隔轮询操作进度）。 例如，当调用 channel.Start() 时，该方法会在频道启动后返回。 还可以使用异步版本：await channel.StartAsync()（有关基于任务的异步模式的信息，请参阅 [TAP](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)。 发送操作请求并且在操作完成之前一直轮询操作状态的 API 称作“轮询方法”。 建议为富客户端应用程序和/或有状态服务使用这些方法（特别是异步版本）。

某些情况下，应用程序不能等待长时运行的 http 请求并且希望手动轮询操作进度。 一个典型的示例是与无状态 web 服务进行交互的浏览器：当浏览器请求创建频道时，web 服务会启动一个长时运行的操作并将操作 ID 返回到浏览器。 然后，浏览器可以根据该 ID 询问 web 服务来获取操作状态。 媒体服务 .NET SDK 提供了非常适用于此情况的 API。 这些 API 称为“非轮询方法”。
“非轮询方法”具有以下命名模式：Send*OperationName*Operation（例如，SendCreateOperation）。 Send*OperationName*Operation 方法返回 **IOperation** 对象；返回的对象包含可以用来跟踪操作的信息。 Send*OperationName*OperationAsync 方法将返回 **Task<IOperation>**。

当前，以下类支持非轮询方法：**Channel**、**StreamingEndpoint** 和 **Program**。

若要轮询操作状态，请对 **OperationBaseCollection** 类使用 **GetOperation** 方法。 使用以下时间间隔来检查操作状态：对于 **Channel** 和 **StreamingEndpoint** 操作，使用 30 秒；对于 **Program** 操作，使用 10 秒。

## <a name="create-and-configure-a-visual-studio-project"></a>创建和配置 Visual Studio 项目

设置开发环境，并根据[使用 .NET 进行媒体服务开发](media-services-dotnet-how-to-use.md)中所述，在 app.config 文件中填充连接信息。

## <a name="example"></a>示例

以下示例定义了一个名为 **ChannelOperations** 的类。 可以将该类定义用作 web 服务类定义的起点。 为简单起见，以下示例使用了方法的非异步版本。

示例还展示了客户端可以如何使用该类。

### <a name="channeloperations-class-definition"></a>ChannelOperations 类定义

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
{
    // Read values from the App.config file.
    private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
    private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
    private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
    private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>客户端代码

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

