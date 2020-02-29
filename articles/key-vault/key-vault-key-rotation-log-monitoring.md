---
title: 使用端到端密钥轮替和审核设置 Azure 密钥保管库 | Microsoft Docs
description: 使用本操作方法指南来帮助你设置密钥轮替和监视密钥保管库日志。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: f7fbc82c08d89d73d671a49fb31b9d3cca01c721
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195509"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>使用密钥轮替和审核设置 Azure Key Vault

## <a name="introduction"></a>介绍

有了密钥保管库以后，即可使用它来存储密钥和机密。 应用程序不再需要保存密钥或机密，可以根据需要从保管库请求密钥或机密。 使用密钥保管库可以更新密钥和机密，而不会影响应用程序的行为，这会为密钥和机密管理提供多种可能性。

>[!IMPORTANT]
> 本文中的示例仅用于说明目的， 它们不用于生产。 

本文介绍：

- 一个使用 Azure Key Vault 存储机密的示例。 在本文中，存储的机密是应用程序访问的 Azure 存储帐户密钥。 
- 如何实现该存储帐户密钥的计划轮换。
- 如何监视密钥保管库审核日志，并在发出意外请求时发出警报。

> [!NOTE]
> 本文不详细介绍密钥保管库的初始设置。 有关信息，请参阅[什么是 Azure 密钥保管库？](key-vault-overview.md)。 有关跨平台命令行接口说明，请参阅[使用 Azure CLI 管理 Key Vault](key-vault-manage-with-cli2.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>设置密钥保管库

要使应用程序能够从密钥保管库检索机密，必须先创建机密并将其上传到保管库。

启动 Azure PowerShell 会话，并使用以下命令登录 Azure 帐户：

```powershell
Connect-AzAccount
```

在弹出的浏览器窗口中，输入 Azure 帐户的用户名和密码。 PowerShell 将获取与此帐户关联的所有订阅。 PowerShell 默认使用第一个订阅。

如果有多个订阅，可能需要指定用来创建密钥保管库的订阅。 输入以下命令查看帐户的订阅：

```powershell
Get-AzSubscription
```

若要指定与要记录的密钥保管库关联的订阅，请输入：

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

因为本文介绍了如何将存储帐户密钥存储为机密，因此，必须获取该存储帐户密钥。

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

检索机密（在本例中为存储帐户密钥）后，必须将该密钥转换为安全字符串，然后在密钥保管库中使用该值创建机密。

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

接下来，获取所创建的机密的 URI。 稍后将需要使用此 URI 来调用密钥保管库并检索机密。 运行以下 PowerShell 命令，并记下 ID 值（即密钥的 URI）：

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>设置应用程序

现在，你已存储了机密，可以使用代码来检索并使用它，然后再执行几个步骤。

首先，必须将应用程序注册到 Azure Active Directory。 然后告诉 Key Vault 您的应用程序信息，使其能够允许来自您的应用程序的请求。

> [!NOTE]
> 必须在与密钥保管库相同的 Azure Active Directory 租户上创建应用程序。

1. 打开**Azure Active Directory**。
2. 选择“应用注册”。 
3. 选择 "**新应用程序注册**"，将应用程序添加到 Azure Active Directory。

    ![在 Azure Active Directory 中打开应用程序](./media/keyvault-keyrotation/azure-ad-application.png)

4. 在 "**创建**" 下，将应用程序类型保留为**WEB 应用/API** ，并为应用程序命名。 为应用程序指定 **"登录 URL"** 。 此 URL 可以是此演示所需的任何内容。

    ![创建应用程序注册](./media/keyvault-keyrotation/create-app.png)

5. 将应用程序添加到 Azure Active Directory 后，应用程序页将打开。 选择 "**设置**"，然后选择 "**属性**"。 复制“应用程序 ID”值。 后面的步骤需要用到它。

接下来，为应用程序生成一个密钥，使其能够与 Azure Active Directory 进行交互。 若要创建密钥，请选择 "**设置**" 下的 "**密钥**"。 记下新生成的 Azure Active Directory 应用程序密钥。 后面的步骤需要用到。 离开此部分后，密钥将不可用。 

![Azure Active Directory 应用密钥](./media/keyvault-keyrotation/create-key.png)

在建立从应用程序到密钥保管库的任何调用之前，必须向密钥保管库告知应用程序及其权限。 以下命令使用保管库名称和 Azure Active Directory 应用中的**应用程序 ID 来授予应用程序**访问密钥保管库的权限。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

你现在可以开始生成应用程序调用了。 在应用程序中，必须安装与 Azure Key Vault 和 Azure Active Directory 交互所需的 NuGet 包。 从 Visual Studio 包管理器控制台中，输入以下命令。 撰写本文时，Azure Active Directory 包的当前版本为3.10.305231913，因此请确认最新版本并根据需要进行更新。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

在应用程序代码中，创建一个类来保存 Azure Active Directory 身份验证的方法。 在本示例中，该类名为“Utils”。 添加以下 `using` 语句：

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

接下来，添加以下方法，从 Azure Active Directory 检索 JWT 令牌。 对于可维护性，你可能想要将硬编码的字符串值移到 web 或应用程序配置中。

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

添加所需的代码，调用密钥保管库并检索机密值。 首先，必须添加以下 `using` 语句：

```csharp
using Microsoft.Azure.KeyVault;
```

添加方法调用，调用密钥保管库并检索机密。 在此方法中，提供在前面步骤中保存的机密 URI。 请注意你之前创建的**Utils**类中的**GetToken**方法的用法。

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

现在，当运行应用程序时，应该会向 Azure Active Directory 进行身份验证，并从 Azure 密钥保管库中检索机密值。

## <a name="key-rotation-using-azure-automation"></a>使用 Azure 自动化进行密钥轮替

> [!IMPORTANT]
> Azure 自动化 runbook 仍要求使用 `AzureRM` 模块。

你现在已准备好为作为 Key Vault 机密存储的值设置轮换策略。 可以通过以下几种方式旋转机密：

- 作为手动过程的一部分
- 使用 API 调用以编程方式
- 通过 Azure 自动化脚本

在本文中，你将使用 PowerShell 和 Azure 自动化来更改 Azure 存储帐户的访问密钥。 然后使用该新密钥更新密钥保管库机密。

若要允许 Azure 自动化在密钥保管库中设置机密值，必须获取名为**AzureRunAsConnection**的连接的客户端 ID。 此连接是在建立 Azure 自动化实例时创建的。 若要查找此 ID，请从 Azure 自动化实例中选择 "**资产**"。 在此处选择 "**连接**"，然后选择 " **AzureRunAsConnection** " 服务主体。 记下**ApplicationId**值。

![Azure 自动化客户端 ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

在 "**资产**" 中，选择 "**模块**"。 选择 "**库**"，然后搜索并导入以下每个模块的更新版本：

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> 在撰写本文时，只需要针对以下脚本更新上面记下的模块。 如果自动化作业失败，请确认已导入所有必要的模块及其依赖项。

检索 Azure 自动化连接的应用程序 ID 之后，必须让密钥保管库知道此应用程序有权更新保管库中的机密。 使用以下 PowerShell 命令：

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

接下来，选择 Azure 自动化实例下的 " **runbook** "，并选择 "**添加 Runbook**"。 选择“快速创建”。 为 runbook 命名，并选择 " **PowerShell** " 作为 "runbook 类型"。 可以添加描述。 最后，选择“创建”。

![创建 Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

将以下 PowerShell 脚本粘贴在新 Runbook 的编辑器窗格中：

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
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

# Optionally you can set the following as parameters
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

在编辑器窗格中，选择 "**测试窗格**" 以测试脚本。 运行脚本后无错误，可以选择 "**发布**"，然后在 "runbook 配置" 窗格中应用 runbook 的计划。

## <a name="key-vault-auditing-pipeline"></a>密钥保管库审核管道

设置密钥保管库时，可以打开审核功能，收集有关对密钥保管库发出的访问请求的日志。 这些日志存储在指定的 Azure 存储帐户中，可以提取、监视和分析。 以下方案使用 Azure 函数、Azure 逻辑应用和密钥保管库审核日志创建一个管道，该管道在与 web 应用的应用 ID 不匹配的应用从保管库中检索机密时发送电子邮件。

首先，必须对密钥保管库启用日志记录。 使用以下 PowerShell 命令。 （[有关密钥保管库的](key-vault-logging.md)详细信息，请参阅本文中的完整详细信息。）

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

启用日志记录后，审核日志将开始存储在指定的存储帐户中。 这些日志包含有关访问密钥保管库的方式、时间和用户的事件。

> [!NOTE]
> 在密钥保管库运行后，有 10 分钟时间访问日志记录信息。 它通常比这更早。

下一步是[创建 Azure 服务总线队列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)。 此队列是密钥保管库审核日志的推送位置。 如果审核日志消息在队列中，则逻辑应用会选取它们并对其进行操作。 使用以下步骤创建服务总线实例：

1. 创建一个服务总线命名空间（如果你已有要使用的命名空间，请跳到步骤2）。
2. 浏览到 Azure 门户中的服务总线实例，并选择要在其中创建队列的命名空间。
3. 选择 "**创建资源** > **企业集成** > **服务总线**"，然后输入所需的详细信息。
4. 通过选择命名空间，然后选择 "**连接信息**" 查找服务总线连接信息。 下一部分将需要此信息。

接下来，[创建 Azure 函数](../azure-functions/functions-create-first-azure-function.md)以轮询存储帐户中的密钥保管库日志并选取新的事件。 将按计划触发此函数。

若要创建 Azure function app，请选择 "**创建资源**"，在 marketplace 中搜索 " **Function App**"，然后选择 "**创建**"。 在创建过程中，可以使用现有的托管计划，或创建新的计划。 你还可以选择动态托管。 有关 Azure Functions 托管选项的详细信息，请参阅[如何缩放 Azure Functions](../azure-functions/functions-scale.md)。

创建 Azure function app 后，请向其进行选择，然后选择该语言的**计时器**方案和**C\#** 。 然后选择 "**创建此函数**"。

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
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
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

            //Required to order by time as they might not be in the file
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
> 更改前面的代码中的变量，以指向写入密钥保管库日志的存储帐户、之前创建的服务总线实例以及密钥保管库存储日志的特定路径。

该函数在写入密钥保管库日志的存储帐户中选取最新日志文件、从该文件中获取最新事件，并将这些事件推送到服务总线队列。 

由于单个文件可以有多个事件，因此，您应创建一个同步 .txt 文件，该函数还会查看此文件，以确定选取的上一个事件的时间戳。 使用此文件可以确保不会多次推送相同的事件。 

同步 .txt 文件包含最后遇到的事件的时间戳。 加载日志时，必须根据时间戳对日志进行排序，以确保其顺序正确。

对于此函数，我们引用了两个不能在 Azure Functions 中使用的框的其他库。 为了包含这些库，我们需要 Azure Functions 使用 NuGet 来请求它们。 在 "**代码**" 框下，选择 "**查看文件**"。

!["查看文件" 选项](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

添加一个名为 "项目 json" 的文件，其中包含以下内容：

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

选择 "**保存**" 后，Azure Functions 将下载所需的二进制文件。

切换到“**集成**”选项卡，为计时器参数指定一个要在函数中使用的有意义名称。 在前面的代码中，函数需要将计时器称为*myTimer*。 为计时器指定[CRON 表达式](../app-service/webjobs-create.md#CreateScheduledCRON)，如下所示： `0 * * * * *`。 此表达式将导致函数一分钟运行一次。

在 "**集成**" 选项卡上，添加类型为 " **Azure Blob 存储**" 的输入。 此输入将指向包含函数查看的最后一个事件的时间戳的同步 .txt 文件。 将使用参数名称在函数内访问此输入。 在上面的代码中，Azure Blob 存储输入要求参数名称为*inputBlob*。 选择将在其中查找同步 .txt 文件的存储帐户（可以是相同或不同的存储帐户）。 在 "路径" 字段中，以 `{container-name}/path/to/sync.txt`格式提供文件的路径。

添加类型为 " **Azure Blob 存储**" 的输出。 此输出将指向在输入中定义的 "同步 .txt" 文件。 函数使用此输出来写入查看的最后一个事件的时间戳。 在上面的代码中，要求此参数名为 *outputBlob*。

函数现在已准备就绪。 确保切换回“开发”选项卡并保存代码。 检查 "输出" 窗口中是否有任何编译错误，并根据需要进行更正。 如果代码编译，则代码现在应每分钟检查密钥保管库日志，并将所有新事件推送到定义的服务总线队列。 每次触发该函数时，都应该看到向日志窗口写入日志记录信息。

### <a name="azure-logic-app"></a>Azure 逻辑应用

接下来，必须创建一个 Azure 逻辑应用，用于选择函数推送到服务总线队列的事件、分析内容，并根据匹配的条件发送电子邮件。

通过选择 "**创建资源**" > **集成** > **逻辑应用**[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

创建逻辑应用后，请向其进行选择，然后选择 "**编辑**"。 在逻辑应用编辑器中，选择 "**服务总线队列**" 并输入服务总线凭据以将其连接到队列。

![Azure 逻辑应用服务总线](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

选择“添加条件”。 在条件中，切换到 "高级编辑器"，然后输入以下代码。 将*APP_ID*替换为 web 应用的实际应用 ID：

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

如果传入事件的*appid* （这是服务总线消息的正文）不是该应用的*appid* ，则此表达式实质上将返回**false** 。

现在，**如果没有，** 请在下创建一个操作。

![Azure 逻辑应用选择操作](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

对于操作，请选择 " **Office 365-发送电子邮件**"。 填写字段，创建当定义的条件返回 **false** 时要发送的电子邮件。 如果没有 Office 365，请查找备选方法以获得相同的结果。

现在，你有了一个端到端管道，用于每分钟查找一次新的密钥保管库审核日志。 它将找到的新日志推送到服务总线队列。 当新消息进入队列中时，将触发逻辑应用。 如果事件内的*appid*与调用应用程序的应用 ID 不匹配，则会发送一封电子邮件。
