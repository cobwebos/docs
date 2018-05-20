---
title: 有关 Azure functions 与 Azure SignalR 服务集成的教程 | Microsoft Docs
description: 本教程介绍如何将 Azure Functions 用于 Azure SignalR 服务
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>教程：Azure Functions 与 Azure SignalR 服务集成

本教程基于前面的教程中创建的聊天室应用程序。 如果尚未完成[使用 SignalR 服务创建聊天室](signalr-quickstart-dotnet-core.md)和 [Azure SignalR 服务身份验证](./signalr-authenticate-oauth.md)，请首先完成这些练习。 

实时应用程序的常见方案是用于将源自服务器的内容更新发布到 Web 客户端。 [Azure Functions](../azure-functions/functions-overview.md) 是生成这些内容更新的绝佳选择。 使用 Azure 函数的主要好处是，可以按需运行代码，而不必担心整个应用程序的体系结构或运行它的基础结构。 此外，仅需为代码实际运行的时间支付费用。  

通常情况下，尝试使用 SignalR 时，此方案会出现问题，因为 SignalR 尝试保持客户端和服务器之间的连接，以推送内容更新。 由于该代码仅按需运行，所以无法保持连接。 但是，Azure SignalR 服务可以支持这种方案，因为它在运行时为你管理连接。

在本教程中，你将使用 Azure Functions 来生成在每分钟的开头使用计时器函数的消息。 该函数会将消息发布到前面的教程中创建的聊天室的所有客户端。 有关计时器函数的详细信息，请参阅[计时器函数](../azure-functions/functions-create-scheduled-function.md)。

可使用任何代码编辑器来完成本快速入门中的步骤。 但是，[Visual Studio Code](https://code.visualstudio.com/) 是一个很好的选项，可用于 Windows、macOS 和 Linux 平台。

本教程所用代码可在 [AzureSignalR-samples GitHub 存储库](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer)下载。

![有服务器消息的聊天应用](./media/signalr-integrate-functions/signalr-functions-complete.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 借助使用 Azure CLI 的 Azure Functions 创建新的计时器函数。
> * 为本地 Git 存储库部署配置计时器函数。
> * 若要每分钟推送更新，请将计时器连接到 SignalR 服务

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>先决条件

若要完成本教程，必须具备以下先决条件：

* [Git](https://git-scm.com/)
* [.NET Core SDK](https://www.microsoft.com/net/download/windows) 
* [已配置的 Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* 下载或克隆 [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) GitHub 存储库。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>创建函数应用

必须创建一个函数应用来定义函数的执行环境。 通过函数应用，还可以将多个函数分组为一个逻辑单元，以便轻松管理、部署和资源共享。 有关详细信息，请参阅[使用 Azure CLI 创建第一个函数](../azure-functions/functions-create-first-azure-function-azure-cli.md)。

在本部分中，你将使用 Azure Cloud Shell 创建新的 Azure Function 应用，为本地 Git 存储库的部署配置。 

创建函数应用资源时，请在以前教程中使用的同一资源组中进行创建。 此方法使得管理所有教程资源更轻松。

将以下脚本复制到文本编辑器，将变量参数的值替换为资源值。 复制更新的脚本并将其粘贴到 Azure Cloud Shell，并按 Enter 创建存储帐户和函数应用。

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| 参数 | 说明 |
| -------------------- | --------------- |
| ResourceGroupName | 这是之前教程中建议的资源组名称。 将所有教程资源聚集在一起是一个好办法。 使用在之前教程中使用的相同资源组。 | 
| location | 将此变量更新到之前教程中用来创建资源组的同一位置。 | 
| functionappName | 将此变量更新为新函数应用的唯一名称。 例如，signalrfunctionapp22665120。 | 
| storageAccountName | 输入用于保存函数应用代码和设置的新存储帐户的名称。 | 



## <a name="configure-the-function-app"></a>配置函数应用

在本部分中，你将使用包含 Azure SignalR 服务资源连接字符串的应用设置配置函数应用。 函数代码将使用此设置连接并将消息发布到聊天室。 还将为本地 Git 存储库的部署配置函数应用。

复制以下脚本，并替换参数值。 将更新的脚本粘贴到 Azure Cloud Shell，并按 Enter。

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| 参数 | 说明 |
| -------------------- | --------------- |
| ResourceGroupName | 这是之前教程中建议的资源组名称。 将所有教程资源聚集在一起是一个好办法。 使用在之前教程中使用的相同资源组。 | 
| functionappName | 将此变量更新为新函数应用的唯一名称。 例如，signalrfunctionapp22665120。 | 
| connstring | 输入 SignalR 服务资源的连接字符串。 通过单击“设置”下的“密钥”，可以从 Azure 门户的 SignalR 服务资源页检索此连接字符串。 | 



记下最后一个命令返回的 Git 部署 URL。 将使用此 URL 部署函数代码。


## <a name="the-timer-function"></a>计时器函数

计时器函数示例位于下载的 /samples/Timer 目录，或者 [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer) github 存储库的克隆。 计时器函数代码位于 TimerFunction.cs。 此代码使用通过默认配置密钥 (Azure:SignalR:ConnectionString) 存储的连接字符串将代理创建到中心。 由于函数代码运行服务器端，因此不需要像常规客户端一样进行身份验证。 信任代码使用连接字符串。 使用此中心代理，函数代码可以调用中心上定义的任何方法。 代码调用 `BroadcastMessage` 方法在触发器触发时发布包含当前时间的消息。

函数代码的触发器是 timerTrigger，在 TimerFunction/function.json 的绑定中进行定义。 它包括 [CRON 表达式](https://wikipedia.org/wiki/Cron#CRON_expression)，该表达式将时间触发器设为在每分钟开头触发。

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>生成计时器函数

使用以下步骤中的 [.NET Core 命令行接口 (CLI)](https://docs.microsoft.com/dotnet/core/tools/) 构建函数并为部署准备它：

1. 导航到下载的 /samples/Timer 子目录，或者 [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) github 存储库的克隆。

2. 使用以下命令还原 NuGet 包：

        dotnet restore

3. 使用以下命令生成“计时器”函数应用：

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>创建和部署本地 Git 存储库

1. 在 Git shell 中，导航到生成子目录，/samples/Timer/bin/Release/net461。

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. 使用以下命令将目录初始化为新的 Git 存储库：

        git init

3. 在生成目录中添加所有文件的新提交。

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. 为配置函数应用过程中记录的 Git 部署 URL 添加远程终结点：

        git remote add Azure <enter your Git deployment URL>

5. 部署函数应用

        git push Azure master

   部署代码后，计时器将立即开始每分钟触发以执行代码。

## <a name="test-the-chat-app"></a>测试聊天应用

导航到聊天应用程序，现在，刚创建的计时器函数将在每分钟开头报时。

![有服务器消息的聊天应用](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>清理资源

如果想要继续测试应用程序，可以保留已创建的资源。

或者，如果已完成示例应用程序，可以删除 Azure 资源避免产生费用。 

> [!IMPORTANT]
> 删除资源组这一操作是不可逆的，资源组以及其中的所有资源会被永久删除。 请确保不会意外删除错误的资源组或资源。 如果是在现有资源组（其中包含要保留的资源）中为托管此示例而创建了相关资源，则可从各自的边栏选项卡逐个删除这些资源，而不是删除资源组。
> 
> 

登录到 [Azure 门户](https://portal.azure.com)，并单击“资源组”。

在“按名称筛选...”文本框中键入资源组的名称。 本文的说明使用名为“SignalRTestResources”的资源组。 在结果列表中的资源组上，单击“...”，然后单击“删除资源组”。

   
![删除](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


系统会要求确认是否删除资源组。 键入资源组的名称进行确认，然后单击“删除”。
   
几分钟后，将删除该资源组及其包含的所有资源。


## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何与 Azure Function 集成，基于 Azure Function 触发器向客户推送更新。 若要了解有关使用 Azure SignalR 服务器的详细信息，请继续了解用于 SignalR 服务的 Azure CLI 示例。

> [!div class="nextstepaction"]
> [Azure SignalR CLI 示例](./signalr-cli-samples.md)



