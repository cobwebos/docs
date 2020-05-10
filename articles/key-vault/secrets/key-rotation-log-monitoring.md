---
title: 使用端到端密钥轮替和审核设置 Azure 密钥保管库 | Microsoft Docs
description: 借助本操作指南设置密钥轮换和监视 Key Vault 日志。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: a5aaef50f12bfec89cf5e883ed6b1c85fa984ad6
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995968"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>使用密钥轮换和审核设置 Azure Key Vault

## <a name="introduction"></a>简介

有了密钥保管库以后，即可用它来存储密钥和机密。 应用程序不再需要保存密钥或机密，但可以根据需要从保管库请求它们。 使用 Key Vault 可以更新密钥和机密，而不会影响应用程序，同时可以各种可能的方法管理密钥和机密。

本文介绍如何实现存储帐户密钥的计划轮换、监视密钥保管库审核日志以及在发出意外请求时引发警报。 

必须首先使用所选方法创建密钥保管库：

- [使用 Azure CLI 在 Azure Key Vault 中设置和检索机密](quick-create-cli.md)
- [使用 Azure PowerShell 在 Azure Key Vault 中设置和检索机密](quick-create-powershell.md)
- [使用 Azure 门户在 Azure Key Vault 中设置和检索机密](quick-create-portal.md)


## <a name="store-a-secret"></a>存储机密

要使应用程序能够从 Key Vault 检索机密，必须先创建机密并将其上传到保管库。

启动 Azure PowerShell 会话，并使用以下命令登录用户的 Azure 帐户：

```powershell
Connect-AzAccount
```

在弹出的浏览器窗口中，输入 Azure 帐户的用户名和密码。 PowerShell 会获取与此帐户关联的所有订阅。 PowerShell 默认使用第一个订阅。

如果有多个订阅，可能需要指定创建 Key Vault 时所用的订阅。 输入以下命令查看帐户的订阅：

```powershell
Get-AzSubscription
```

若要指定与要记录的 Key Vault 关联的订阅，请输入：

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

因为本文介绍了如何将存储帐户密钥存储为机密，因此，必须获取该存储帐户密钥。

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

检索用户的机密（在本例中，为存储帐户密钥）后，必须将该密钥转换为安全字符串，并在 Key Vault 中使用该值创建机密。

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

接下来，获取你创建的机密的 URI。 在稍后的步骤中调用 Key Vault 和检索机密时，需要用到此 URI。 运行以下 PowerShell 命令，并记下 ID 值（即机密 URI）：

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>设置应用程序

存储机密后，可以在执行几个附加步骤后，使用代码检索并使用该机密。

首先必须将应用程序注册到 Azure Active Directory。 然后向 Key Vault 告知应用程序信息，使其允许来自应用程序的请求。

> [!NOTE]
> 必须在与 Key Vault 相同的 Azure Active Directory 租户上创建应用程序。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 如果帐户有权访问多个租户，请在右上角选择该帐户。 将门户会话设置为所需的 Azure AD 租户。
1. 搜索并选择“Azure Active Directory”  。 在“管理”  下，选择“应用注册”  。
1. 选择“新注册”。 
1. 在“注册应用程序”  中，输入一个要向用户显示的有意义的应用程序名称。
1. 指定可使用应用程序的用户，如下所示：

    | 支持的帐户类型 | 说明 |
    |-------------------------|-------------|
    | **仅此组织目录中的帐户** | 若要生成业务线 (LOB) 应用程序，请选择此选项。 如果不在目录中注册应用程序，则此选项不可用。<br><br>此选项映射到仅限 Azure AD 的单租户。<br><br>此选项是默认选项，除非你是在目录外部注册应用。 如果在目录外部注册应用，则默认设置为 Azure AD 多租户和个人 Microsoft 帐户。 |
    | **任何组织目录中的帐户** | 若要面向所有企业和教育客户，请选择此选项。<br><br>此选项映射到仅限 Azure AD 的多租户。<br><br>如果已将应用注册为仅限 Azure AD 的单租户，则可通过“身份验证”页将其更新为 Azure AD 多租户，以及从多租户更新为单租户。  |
    | **任何组织目录中的帐户和个人 Microsoft 帐户** | 若要面向最广泛的客户，请选择此选项。<br><br>此选项映射到 Azure AD 多租户和个人 Microsoft 帐户。<br><br>如果已将应用注册为 Azure AD 多租户和个人 Microsoft 帐户，则不能在 UI 中更改此设置， 而只能使用应用程序清单编辑器来更改支持的帐户类型。 |

1. 在“重定向 URI (可选)”  下，选择要生成的应用的类型：“Web”或“公共客户端(移动和桌面)”。   然后，输入应用程序的重定向 URI 或回复 URL。

    * 对于 Web 应用程序，请提供应用的基 URL。 例如，`https://localhost:31544` 可以是本地计算机上运行的 Web 应用的 URL。 用户将使用此 URL 登录到 Web 客户端应用程序。
    * 对于公共客户端应用程序，请提供 Azure AD 返回令牌响应时所用的 URI。 输入特定于应用程序的值，例如 `myapp://auth`。

1. 完成后，选择“注册”  。

    ![显示了在 Azure 门户中注册新应用程序的屏幕](../media/new-app-registration.png)

Azure AD 会向应用分配唯一的应用程序 ID 或客户端 ID。 门户会打开应用程序的“概览”页。  请注意**应用程序（客户端） ID**值。

若要向应用程序添加功能，可以选择其他配置选项，包括品牌、证书和机密、API 权限等。

![新注册应用的概览页示例](../media//new-app-overview-page-expanded.png)

在建立从应用程序到 Key Vault 的任何调用之前，必须让 Key Vault 知道应用程序及其权限。 以下命令使用 "保管库名称" 和 "**应用程序（客户端） ID" （** 来自 Azure Active Directory 应用）授予**应用程序访问**密钥保管库的权限。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

现在可以开始生成应用程序调用。 在应用程序中，必须安装所需的 NuGet 包，以便与 Azure Key Vault 和 Azure Active Directory 交互。 从 Visual Studio 包管理器控制台输入以下命令。 在编写本文时，Azure Active Directory 包的最新版本为 3.10.305231913，请确认最新版本并视需要进行更新。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

在应用程序代码中，创建一个类来保存 Azure Active Directory 身份验证的方法。 在本示例中，该类名为 **Utils**。 添加以下 `using` 语句：

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

接下来，添加以下方法，从 Azure Active Directory 检索 JWT 令牌。 为了方便维护，请将硬编码的字符串值移到 Web 或应用程序配置。

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

添加方法调用，调用密钥保管库并检索机密。 在此方法中，提供在前面步骤中保存的机密 URI。 请注意如何使用前面创建的 **Utils** 类中的 **GetToken** 方法。

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

现在，运行应用程序时，用户应该会向 Azure Active Directory 进行身份验证，并从 Azure Key Vault 中检索机密值。

## <a name="key-rotation-using-azure-automation"></a>使用 Azure 自动化进行密钥轮替

> [!IMPORTANT]
> Azure 自动化 Runbook 仍需使用 `AzureRM` 模块。

现在，对于存储为 Key Vault 机密的值，可以设置轮换策略。 可通过多种方式轮换机密：

- 手动轮换
- 使用 API 调用以编程方式轮换
- 通过 Azure 自动化脚本轮换

本文结合使用 Azure PowerShell 和 Azure 自动化来更改 Azure 存储帐户的访问密钥。 然后使用新密钥更新 Key Vault 机密。

若要允许 Azure 自动化在 Key Vault 中设置机密值，必须获取名为 **AzureRunAsConnection** 的连接的客户端 ID。 此连接是建立 Azure 自动化实例时创建的。 若要查找此 ID，请在 Azure 自动化实例中选择“资产”。  在此处选择“连接”，然后选择“AzureRunAsConnection”服务主体。   记下“ApplicationId”值。 

![Azure 自动化客户端 ID](../media/keyvault-keyrotation/Azure_Automation_ClientID.png)

在“资产”中选择“模块”。   选择“库”，然后搜索并导入以下每个模块的更新版本： 

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> 在撰写本文时，只需要针对以下脚本更新上面记下的模块。 如果自动化作业失败，请确认已导入所有必要的模块及其依赖项。

检索 Azure 自动化连接的应用程序 ID 之后，必须让 Key Vault 知道此应用程序有权更新保管库中的机密。 使用以下 PowerShell 命令：

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

接下来，选择 Azure 自动化实例下的“Runbook”，然后选择“添加 Runbook”。   选择“快速创建”。  为 Runbook 命名，然后选择“PowerShell”作为 Runbook 类型。  可以添加说明。 最后，选择“创建”  。

![创建 Runbook](../media/keyvault-keyrotation/Create_Runbook.png)

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

在编辑器窗格中，选择“测试”窗格以测试脚本。  正常运行脚本后，可以选择“发布”，并在 Runbook 配置窗格中应用 Runbook 的计划。 

## <a name="key-vault-auditing-pipeline"></a>密钥保管库审核管道

设置密钥保管库时，可以打开审核功能，收集有关对密钥保管库发出的访问请求的日志。 这些日志存储在指定的 Azure 存储帐户中，可以提取、监视和分析。 以下方案将使用 Azure Functions、Azure 逻辑应用和 Key Vault 审核日志创建管道，以便在与该 Web 应用的应用 ID 不匹配的应用从保管库检索机密时发送电子邮件。

首先，必须对密钥保管库启用日志记录。 使用以下 PowerShell 命令。 （可以在[这篇有关 Key Vault 日志记录的文章](../general/logging.md)中查看完整详细信息。）

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

启用日志记录后，审核日志将开始存储到指定的存储帐户中。 这些日志包含有关访问密钥保管库的方式、时间和用户的事件。

> [!NOTE]
> 在执行 Key Vault 操作 10 分钟后，即可访问日志记录信息。 但通常不用等待这么长时间。

下一步是[创建 Azure 服务总线队列](../../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)。 此队列是 Key Vault 审核日志的推送位置。 审核日志消息进入队列后，逻辑应用将选择并处理它们。 使用以下步骤创建服务总线实例：

1. 创建服务总线命名空间（如果已有一个可用的命名空间，请跳到步骤 2）。
2. 在 Azure 门户中浏览到服务总线实例，并选择要在其中创建队列的命名空间。
3. 选择“创建资源” > “企业集成” > “服务总线”，并输入所需的详细信息。   
4. 通过选择命名空间并选择“连接信息”  ，找到服务总线连接信息。 在下一部分需要用到此信息。

接下来，[创建 Azure 函数](../../azure-functions/functions-create-first-azure-function.md)以轮询存储帐户中的 Key Vault 日志并选取新的事件。 此函数将按计划触发。

若要创建 Azure 函数应用，请选择“创建资源”  ，在市场中搜索“函数应用”  ，并选择“创建”  。 在创建过程中，可以使用现有的托管计划，或创建新的计划。 也可以选择动态托管。 有关 Azure Functions 的托管选项的详细信息，请参阅 [如何缩放 Azure Functions](../../azure-functions/functions-scale.md)。

创建 Azure 函数应用后，转到该应用，然后选择“计时器”作为方案，选择“C\#”作为语言。   然后选择“创建此函数”  。

![Azure Functions“开始”屏幕边栏选项卡](../media/keyvault-keyrotation/Azure_Functions_Start.png)

在“开发”选项卡中，将 run.csx 代码替换为以下内容： 

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
> 更改上面代码中的变量，以指向写入 Key Vault 日志的存储帐户、以前创建的服务总线实例和 Key Vault 存储日志的特定路径。

该函数在写入密钥保管库日志的存储帐户中选取最新日志文件、从该文件中获取最新事件，并将这些事件推送到服务总线队列。 

由于单个文件可以包含多个事件，因此应该创建了一个 sync.txt 文件，函数会参照该文件确定所选最后一个事件的时间戳。 使用此文件可以确保不会多次推送相同的事件。 

sync.txt 文件包含上次遇到的事件的时间戳。 加载日志时，必须根据其时间戳将其排序，以确保其顺序正确。

在此函数中，我们引用了 Azure Functions 中几个无法现成使用的附加库。 若要包含这些库，需要在 Azure Functions 中使用 NuGet 提取它们。 在“代码”框下，选择“查看文件”。  

![“查看文件”选项](../media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

添加包含以下内容的名为 project.json 的文件：

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

选择“保存”  后，Azure Functions 将下载所需的二进制文件。

切换到“**集成**”选项卡，为计时器参数指定一个要在函数中使用的有意义名称。 在以上代码中，函数需要名为 *myTimer* 的计时器。 按如下所示为计时器指定 [CRON 表达式](../../app-service/webjobs-create.md#CreateScheduledCRON)：`0 * * * * *`。 此表达式会导致函数一分钟运行一次。

在同一个“集成”  选项卡上，添加类型为“Azure Blob 存储”  的输入。 此输入会指向 sync.txt 文件，其中包含该函数查看的最后一个事件的时间戳。 将在函数中使用参数名称访问此输入。 在以上代码中，Azure Blob 存储输入要求参数名称为 *inputBlob*。 选择 sync.txt 文件所在的存储帐户（该存储帐户可以相同，也可以不同）。 在路径字段中，以 `{container-name}/path/to/sync.txt` 格式提供文件的路径。

添加一个类型为“Azure Blob 存储”  的输出。 此输出会指向刚在输入中定义的 sync.txt 文件。 函数使用此输出写入所查找的最后一个事件的时间戳。 在上面的代码中，要求此参数名为 *outputBlob*。

函数现已准备就绪。 确保切换回“开发”  选项卡并保存代码。 检查输出窗口中是否有任何编译错误并根据需要进行更正。 如果代码可以编译，则代码现在应会每隔一分钟检查 Key Vault 日志，并将所有新事件推送到定义的服务总线队列。 每次触发该函数时，应该都会看到向日志窗口写入日志记录信息。

### <a name="azure-logic-app"></a>Azure 逻辑应用

接下来，必须创建一个 Azure 逻辑应用，用于选择函数推送到服务总线队列的事件、分析内容，并根据匹配的条件发送电子邮件。

选择“创建资源” > “集成” > “逻辑应用”来[创建逻辑应用](../../logic-apps/quickstart-create-first-logic-app-workflow.md)。   

创建逻辑应用后，转到该应用并选择“编辑”。  在逻辑应用编辑器中，选择“服务总线队列”  ，并输入服务总线凭据以将其连接到队列。

![Azure 逻辑应用服务总线](../media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

选择“添加条件”。  在条件中，切换到高级编辑器并输入以下代码。 将 *APP_ID* 替换为 Web 应用的实际应用 ID：

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

如果传入事件中的 *appid*（这是服务总线消息的正文）不是该应用的 *appid*，则此表达式实质上将返回 **false**。

现在，在“如果否，则不执行任何操作”  下创建一个操作。

![在 Azure 逻辑应用中选择操作](../media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

对于操作，选择“Office 365 - 发送电子邮件”  。 **false**时要发送的电子邮件。 如果没有 Office 365，请查看能够实现相同结果的替代方案。

现在端到端管道已创建完毕，它每分钟都会查找一次是否有新的 Key Vault 审核日志。 它将发现的新日志推送到服务总线队列。 新消息进入队列后，会触发逻辑应用。 如果事件中的 *appid* 与调用方应用程序的应用 ID 不匹配，该管道将发送电子邮件。
