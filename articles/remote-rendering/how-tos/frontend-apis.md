---
title: 用于身份验证的 Azure 前端 API
description: 说明如何使用 C# 前端 API 进行身份验证
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681345"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>使用 Azure 前端 API 进行身份验证

在本节中，我们将介绍如何使用 C# API 进行身份验证。

## <a name="azurefrontendaccountinfo"></a>Azure 前端帐户信息

Azure Frontend 帐户信息用于设置 SDK 中```AzureFrontend```实例的身份验证信息。

重要领域包括：

```cs

    public class AzureFrontendAccountInfo
    {
        // Something akin to "<region>.mixedreality.azure.com"
        public string AccountDomain;

        // Can use one of:
        // 1) ID and Key.
        // 2) AuthenticationToken.
        // 3) AccessToken.
        public string AccountId = Guid.Empty.ToString();
        public string AccountKey = string.Empty;
        public string AuthenticationToken = string.Empty;
        public string AccessToken = string.Empty;
    }

```

对于域中的_区域_部分，请使用[您附近的区域](../reference/regions.md)。

帐户信息可以从门户获取，如[检索帐户信息](create-an-account.md#retrieve-the-account-information)段落中所述。

## <a name="azure-frontend"></a>Azure 前端

相关类是```AzureFrontend```和```AzureSession```。 ```AzureFrontend```用于帐户管理和帐户级别功能，包括：资产转换和呈现会话创建。 ```AzureSession```用于会话级功能，包括：会话更新、查询、续订和停用。

每个打开/创建的```AzureSession```都将保留对创建它的前端的引用。 要干净地关闭，所有会话都必须在前端处理之前进行处理。

取消分配会话不会停止 Azure 上的 VM，`AzureSession.StopAsync`必须显式调用。

创建会话并将其状态标记为就绪后，它可以使用 连接到远程呈现运行时`AzureSession.ConnectToRuntime`。

### <a name="threading"></a>线程

所有 AzureSession 和 Azure Frontend 异步调用都在后台线程中完成，而不是主应用程序线程。

### <a name="conversion-apis"></a>转换 API

有关转换服务的详细信息，请参阅[模型转换 REST API](conversion/conversion-rest-api.md)。

#### <a name="start-asset-conversion"></a>启动资产转换

``` cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string modelName, string modelUrl, string assetContainerUrl)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionParams(modelName, modelUrl, assetContainerUrl));
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

#### <a name="get-conversion-status"></a>获取转化状态

``` cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

### <a name="rendering-apis"></a>渲染 API

有关[会话管理的详细信息，请参阅会话管理 REST API。](session-rest-api.md)

渲染会话可以在服务上动态创建，也可以将已有的会话 ID"打开"到 AzureSession 对象中。

#### <a name="create-rendering-session"></a>创建渲染会话

``` cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="open-an-existing-rendering-session"></a>打开现有呈现会话

打开现有会话是同步调用。

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>获取当前呈现会话

``` cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

### <a name="session-apis"></a>会话 API

#### <a name="get-rendering-session-properties"></a>获取呈现会话属性

``` cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="update-rendering-session"></a>更新呈现会话

``` cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="stop-rendering-session"></a>停止渲染会话

``` cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="connect-to-arr-inspector"></a>连接到 ARR 检查器

``` cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

## <a name="next-steps"></a>后续步骤

* [创建帐户](create-an-account.md)
* [电源外壳脚本示例](../samples/powershell-example-scripts.md)
