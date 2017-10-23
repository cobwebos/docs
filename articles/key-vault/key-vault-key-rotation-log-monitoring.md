---
title: "使用端到端密钥轮替和审核设置 Azure 密钥保管库 | Microsoft Docs"
description: "使用本操作指南帮助设置密钥轮替和监视密钥保管库日志。"
services: key-vault
documentationcenter: 
author: swgriffith
manager: mbaldwin
tags: 
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: jodehavi;stgriffi
ms.openlocfilehash: f98ba1e2da6924476392948a4d18c807d68e39e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>使用端到端密钥轮替和审核设置 Azure 密钥保管库
## <a name="introduction"></a>介绍
创建密钥保管库后，可以开始使用该保管库存储密钥和机密。 应用程序不再需要保存密钥或机密，而会根据需要从密钥保管库请求密钥或机密。 这样，便可以更新密钥和机密，不会影响应用程序的行为，同时可以各种可能的方法管理密钥和机密。

本文介绍了一个使用 Azure 密钥保管库存储机密的示例；在本例中，为应用程序将访问的 Azure 存储帐户密钥。 还介绍了该存储帐户密钥的计划轮替的实现。 最后，介绍了如何监视密钥保管库审核日志，并在收到意外请求时发出警报。

> [!NOTE]
> 本教程不详细说明密钥保管库的初始设置。 有关这方面的信息，请参阅 [Get started with Azure Key Vault](key-vault-get-started.md)（Azure 密钥保管库入门）。 有关跨平台命令行接口说明，请参阅[使用 CLI 管理密钥保管库](key-vault-manage-with-cli2.md)。
>
>

## <a name="set-up-key-vault"></a>设置密钥保管库
要使应用程序能够从密钥保管库检索机密，必须先创建机密并将其上传到保管库。 此操作可通过以下方式实现：启动 Azure PowerShell 会话，然后使用以下命令登录 Azure 帐户：

```powershell
Login-AzureRmAccount
```

在弹出的浏览器窗口中，输入 Azure 帐户用户名和密码。 PowerShell 将获取与此帐户关联的所有订阅。 PowerShell 默认使用第一个订阅。

如果有多个订阅，可能需要指定用来创建密钥保管库的订阅。 输入以下命令查看帐户的订阅：

```powershell
Get-AzureRmSubscription
```

要指定与要记录的密钥保管库关联的订阅，请输入：

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

因为本文介绍了如何将存储帐户密钥存储为机密，因此，必须获取该存储帐户密钥。

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

检索机密（在本例中，为存储帐户密钥）后，必须将该机密转换为安全字符串，然后在密钥保管库中使用该值创建机密。

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
接下来，获取所创建的机密的 URI。 这会在后续步骤中使用，在该步骤中将调用密钥保管库检索机密。 运行以下 PowerShell 命令，并记下 ID 值（即机密 URI）：

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>设置应用程序
存储机密后，可使用代码检索并使用它。 需要执行几个步骤才能实现此目的。 第一个也是最重要的步骤是向 Azure Active Directory 注册应用程序，并让密钥保管库知道应用程序的信息，以便允许来自应用程序的请求。

> [!NOTE]
> 必须在与密钥保管库相同的 Azure Active Directory 租户上创建应用程序。
>
>

打开 Azure Active Directory 的“应用程序”选项卡。

![在 Azure Active Directory 中打开应用程序](./media/keyvault-keyrotation/AzureAD_Header.png)

选择“ADD”，向 Azure Active Directory 添加应用程序。

![选择“ADD”](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

将应用程序类型保留为“Web 应用程序和/或 WEB API”，然后为应用程序命名。

![为应用程序命名](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

为应用程序指定“登录 URL”和“应用 ID URI”。 可以在本演示中随意填写这些信息，以后也可以根据需要更改。

![提供所需的 URI](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

将应用程序添加到 Azure Active Directory 后，会转到应用程序页。 单击“配置”选项卡，并查找并复制“客户端 ID”值。 记下客户端 ID 以供后续步骤使用。

接下来，为应用程序生成密钥，以便它可以与 Azure Active Directory 交互。 可以在“配置”选项卡的“密钥”部分下创建此密钥。记下在 Azure Active Directory 应用程序中新生成的密钥，以供后续步骤使用。

![Azure Active Directory 应用密钥](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

在建立从应用程序到密钥保管库的任何调用之前，必须让密钥保管库知道应用程序及其权限。 以下命令从 Azure Active Directory 应用获取保管库名称和客户端 ID，并为应用程序授予对密钥保管库的“Get”权限。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

现在便可开始生成应用程序调用了。 在应用程序中，必须安装所需的 NuGet 包，以便与 Azure 密钥保管库和 Azure Active Directory 交互。 从 Visual Studio 包管理器控制台中，输入以下命令。 在编写本文时，Azure Active Directory 包的最新版本为 3.10.305231913，因此你可能想要确认最新版本并相应地更新。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

在应用程序代码中，创建一个类来保存 Azure Active Directory 身份验证的方法。 在本示例中，该类名为“Utils”。 添加以下 using 语句：

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

接下来，添加以下方法，从 Azure Active Directory 检索 JWT 令牌。 为了方便维护，请将硬编码的字符串值移到 Web 或应用程序配置中。

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

添加所需的代码，调用密钥保管库并检索机密值。 首先，必须添加以下 using 语句：

```csharp
using Microsoft.Azure.KeyVault;
```

添加方法调用，调用密钥保管库并检索机密。 在此方法中，提供在前面步骤中保存的机密 URI。 请注意如何使用以前创建的“Utils”类中的 **GetToken** 方法。

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

现在，运行应用程序时，应该会向 Azure Active Directory 进行身份验证，然后从 Azure 密钥保管库中检索机密值。

## <a name="key-rotation-using-azure-automation"></a>使用 Azure 自动化进行密钥轮替
对于存储为 Azure 密钥保管库机密的值，可以使用多种选项实现其轮替策略。 机密可以手动轮替、使用 API 调用以编程方式轮替，或者通过自动化脚本轮替。 为实现本文的目的，我们将结合使用 Azure PowerShell 与 Azure 自动化更改 Azure 存储帐户访问密钥。 然后使用该新密钥更新密钥保管库机密。

若要允许 Azure 自动化在密钥保管库中设置机密值，必须获取建立 Azure 自动化实例时所创建的名为“AzureRunAsConnection”的连接的客户端 ID。 可以通过从 Azure 自动化实例中选择“资产”获取此 ID。 在此处选择“连接”，并选择“AzureRunAsConnection”服务主体。 请记下“应用程序 ID”。

![Azure 自动化客户端 ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

在“资产”中，选择“模块”。 在“模块”中选择“库”，并搜索并“导入”以下每个模块的更新版本：

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> 在撰写本文时，只需要针对以下脚本更新上面记下的模块。 如果发现自动化作业失败，确认已导入所有必要的模块及其依赖项。
>
>

检索 Azure 自动化连接的应用程序 ID 之后，必须让密钥保管库知道此应用程序有权更新保管库中的机密。 可以使用以下 PowerShell 命令实现此目的：

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

接下来，选择 Azure 自动化实例下面的“Runbook”，并选择“添加 Runbook”。 选择“快速创建”。 为 Runbook 命名，并选择“PowerShell”作为 Runbook 类型。 可以选择添加说明。 最后，单击“创建”。

![创建 Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

将以下 PowerShell 脚本粘贴在新 Runbook 的编辑器窗格中：

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

在编辑器窗格中，选择“测试窗格”测试脚本。 正常运行脚本后，可以选择“发布”，并返回 Runbook 的配置窗格以应用 Runbook 的计划。

## <a name="key-vault-auditing-pipeline"></a>密钥保管库审核管道
设置密钥保管库时，可以打开审核功能，收集有关对密钥保管库发出的访问请求的日志。 这些日志存储在指定的 Azure 存储帐户中，可以提取、监视和分析。 以下方案将使用 Azure Functions、Azure 逻辑应用和密钥保管库审核日志创建管道，在与 Web 应用的应用 ID 匹配的应用从保管库检索机密时发送电子邮件。

首先，必须对密钥保管库启用日志记录。 这可以通过以下 PowerShell 命令完成（有关完整详细信息，可以查看 [key-vault-logging](key-vault-logging.md)）：

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

启用日志记录后，审核日志将开始收集到指定的存储帐户中。 这些日志包含有关访问密钥保管库的方式、时间和用户的事件。

> [!NOTE]
> 在密钥保管库运行后，有 10 分钟时间访问日志记录信息。 访问速度通常会更快。
>
>

下一步是[创建 Azure 服务总线队列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)。 这是密钥保管库审核日志的推送位置。 当审核日志消息位于队列中时，逻辑应用将选取并处理它们。 通过以下步骤创建服务总线：

1. 创建服务总线命名空间（如果要在本示例中使用现有的命名空间，请跳到步骤 2）。
2. 在 Azure 门户中浏览到服务总线，并选择要在其中创建队列的命名空间。
3. 选择“新建”，依次选择“服务总线”>“队列”并输入所需的详细信息。
4. 通过选择命名空间并单击“连接信息”，获取服务总线连接信息。 在下一部分需要用到此信息。

接下来，[创建 Azure 函数](../azure-functions/functions-create-first-azure-function.md)以轮询存储帐户中的密钥保管库日志并选取新的事件。 这是一个按计划触发的函数。

若要创建 Azure 函数，请在 Azure 门户中依次选择“新建”>“Function App”。 在创建过程中，可以使用现有的托管计划，或创建新的计划。 也可以选择动态托管。 有关托管选项的函数的更多详细信息，请参阅[如何缩放 Azure Functions](../azure-functions/functions-scale.md)。

创建 Azure 函数后，导航到它并选择计时器函数和 C\#。 然后单击“创建此函数”。

![Azure Functions“开始”屏幕边栏选项卡](./media/keyvault-keyrotation/Azure_Functions_Start.png)

在“开发”选项卡上，将 run.csx 代码替换为以下内容：

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```


> [!NOTE]
> 确保替换上面代码中的变量，以指向写入密钥保管库日志的存储帐户、以前创建的服务总线和密钥保管库存储日志的特定路径。
>
>

该函数在写入密钥保管库日志的存储帐户中选取最新日志文件、从该文件中获取最新事件，并将这些事件推送到服务总线队列。 由于单个文件可以有多个事件，因此我们创建 sync.txt 文件，该函数还要查看该文件以确定选取最后一个事件的时间戳。 这可以确保不会多次推送相同的事件。 此 sync.txt 文件包含最后一个事件的时间戳。 加载日志时，必须根据该时间戳将日志排序，以确保其顺序正确。

在此函数中，我们引用了 Azure Functions 中几个无法现成使用的附加库。 为了包含这些库，需要 Azure Functions 来使用 nuget 提取它们。 选择“查看文件”选项。

![“查看文件”选项](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

并添加包含以下内容的名为 project.json 的文件：

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
单击“保存”后，Azure Functions 将下载必需的二进制文件。

切换到“**集成**”选项卡，为计时器参数指定一个要在函数中使用的有意义名称。 在上面的代码中，需要称为 *myTimer* 的计时器。 按如下所示为计时器指定 [CRON 表达式](../app-service/web-sites-create-web-jobs.md#CreateScheduledCRON)：0 \*\*\*\*\*，这会导致函数一分钟运行一次。

在同一个“集成”选项卡上，添加类型为“Azure Blob 存储”的输入。 这会指向 sync.txt 文件，其中包含该函数查看的最后一个事件的时间戳。 将在函数中按参数名称提供此文件。 在上面的代码中，Azure Blob 存储输入要求参数名称为 *inputBlob*。 选择将存储 sync.txt 文件的存储帐户（该存储帐户可以相同，也可以不同）。 在路径字段中，提供采用 {container-name}/path/to/sync.txt 格式存储文件的路径。

添加一个类型为“Azure Blob 存储”输出的输出。 这会指向刚在输入中定义的 sync.txt 文件。 函数将使用此文件写入所查找的最后一个事件的时间戳。 在上面的代码中，要求此参数名为 *outputBlob*。

现在，函数已准备就绪。 确保切换回“开发”选项卡并保存代码。 检查输出窗口中是否有任何编译错误并相应地更正。 如果代码可以编译，则代码现在应每隔一分钟检查密钥保管库日志，并将所有新事件推送到定义的服务总线队列。 每次触发该函数时，都应该看到向日志窗口写入日志记录信息。

### <a name="azure-logic-app"></a>Azure 逻辑应用
接下来，必须创建一个 Azure 逻辑应用，用于选取函数推送到服务总线队列的事件、分析内容，并根据匹配的条件发送电子邮件。

通过转到“新建”>“逻辑应用”，[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

创建逻辑应用后，导航到它，并选择“编辑”。 在逻辑应用编辑器中，选择“服务总线队列”，并输入服务总线凭据以将其连接到队列。

![Azure 逻辑应用服务总线](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

接下来，选择“添加条件”。 在条件中，切换到高级编辑器并输入以下代码，将 APP_ID 替换为 Web 应用的实际 APP_ID：

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

如果传入事件中的 *appid*（这是服务总线消息的正文）不是该应用的 *appid*，则此表达式实质上将返回 **false**。

现在，在“如果否，则不执行任何操作”下创建一个操作。

![在 Azure 逻辑应用中选择操作](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

对于操作，选择“**Office 365 - 发送电子邮件**”。 填写字段，创建当定义的条件返回 **false** 时要发送的电子邮件。 如果没有 Office 365，可以查看能够达到相同效果的替代方案。

现在端到端管道已创建完毕，它每分钟都会查找一次是否有新的密钥保管库审核日志。 它将发现的新日志推送到服务总线队列。 当新消息进入队列中时，将触发逻辑应用。 如果事件内的 *appid* 与调用应用程序的应用 ID 不匹配，将发送一封电子邮件。
