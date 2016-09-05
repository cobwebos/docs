<properties
	pageTitle="如何使用端到端密钥轮替和审核设置密钥保管库 | Microsoft Azure"
	description="借助本操作指南来设置密钥轮替和监视密钥保管库日志"
	services="key-vault"
	documentationCenter=""
	authors="swgriffith"
	manager=""
	tags=""/>

<tags
	ms.service="key-vault"
	ms.date="07/05/2016"
	wacn.date=""/>  


#如何使用端到端密钥轮替和审核设置密钥保管库

##介绍

在创建 Azure 密钥保管库之后，可以开始利用该保管库来存储密钥和机密。应用程序不再需要保存密钥或机密，而会根据需要从密钥保管库请求密钥或机密。这样，你便可以更新密钥和机密，不会影响应用程序的行为，同时可以各种可能的方法来管理密钥和机密。

本文将演练一个利用 Azure 密钥保管库来存储机密的示例。在本例中，应用程序将访问 Azure 存储帐户机密。文中还将演示如何实现该存储帐户机密的计划轮替。最后，本文将逐步演示如何监视密钥保管库审核日志，并在收到意外的请求时发出警报。

> [AZURE.NOTE] 本教程不会详细说明 Azure 密钥保管库的初始设置。有关这方面的信息，请参阅 [Get started with Azure Key Vault](/documentation/articles/key-vault-get-started/)（Azure 密钥保管库入门）。或者，有关跨平台命令行接口说明，请参阅[此对应教程](/documentation/articles/key-vault-manage-with-cli/)。

##设置密钥保管库

若要使应用程序能够从 Azure 密钥保管库检索机密，必须先创建机密并将其上载到保管库。通过如下所示的 PowerShell 可轻松实现此目的。

启动 Azure PowerShell 会话，然后使用以下命令登录你的 Azure 帐户：

powershell

	Login-AzureRmAccount -EnvironmentName AzureChinaCloud


在弹出的浏览器窗口中，输入你的 Azure 帐户用户名和密码。Azure PowerShell 会获取与此帐户关联的所有订阅，并按默认使用第一个订阅。

如果你有多个订阅，可能需要指定用来创建 Azure 密钥保管库的特定订阅。键入以下命令查看帐户的订阅：

powershell

	Get-AzureRmSubscription


然后，键入以下命令以指定与要记录的密钥保管库关联的订阅：

powershell

	Set-AzureRmContext -SubscriptionId <subscriptionID> 


本文演示如何将存储帐户密钥存储为机密，而你需要获取该存储帐户密钥。

powershell

	Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>


在检索机密（在本例中为你的存储帐户密钥）之后，必须将其转换为安全字符串，然后使用该值在密钥保管库中创建机密。

powershell

	$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force
	
	Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue

接下来，需要获取刚刚创建的机密的 URI。在后面的步骤中，当你调用密钥保管库检索机密时，会用到该 URI。运行以下 PowerShell 命令并记下“Id”值，即机密 URI。

powershell

	Get-AzureKeyVaultSecret –VaultName <vaultName>


##设置应用程序

现在你存储了机密，接下来需要检索该机密并通过代码来使用它。需要执行几个步骤才能实现此目的，第一个也是最重要的步骤是向 Azure Active Directory 注册应用程序，然后让 Azure 密钥保管库知道应用程序的信息，以便允许来自应用程序的请求。

> [AZURE.NOTE] 必须在与密钥保管库相同的 Azure Active Directory 租户上创建应用程序。

首先打开 Azure Active Directory 的“应用程序”选项卡

![在 Azure AD 中打开“应用程序”](./media/keyvault-keyrotation/AzureAD_Header.png)

选择“添加”，将新应用程序添加到 Azure AD

![选择“添加应用程序”](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

将应用程序类型保留为“Web 应用程序和/或 WEB API”，然后为应用程序指定名称。

![为应用程序命名](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

为应用程序指定“登录 URL”和“应用 ID URI”。这些信息可以是你想要在本演示中使用的任何内容，并且以后可以根据需要更改。

![提供所需的 URI](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

将应用程序添加到 Azure AD 后，你将转到应用程序页。此时，请单击“配置”选项卡，然后查找并复制“客户端 ID”值。记下客户端 ID 以供后续步骤使用。

接下来，需要生成密钥，以便应用程序能够与你的 Azure AD 交互。可以在“配置”选项卡的“密钥”部分下面创建此密钥。记下在 Azure AD 应用程序中新生成的密钥，以供后续步骤使用。

![Azure AD 应用密钥](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

在建立从你的应用程序到密钥保管库的任何调用之前，需要让密钥保管库知道你的应用程序及其权限。以下命令将从 Azure AD 应用获取保管库名称和客户端 ID，并为应用程序授予对密钥保管库的“Get”权限。

powershell

	Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get



现在，你便可以开始构建应用程序调用了。在应用程序中，首先需要安装所需的 NuGet 包，以便与 Azure 密钥保管库和 Azure Active Directory 交互。从 Visual Studio 包管理器控制台输入以下命令。请注意，在编写本文时，ActiveDirectory 包的最新版本为 3.10.305231913，因此请确认最新版本并相应地更新。

powershell

	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913
	
	Install-Package Microsoft.Azure.KeyVault


在应用程序代码中，创建一个类来保存 Active Directory 身份验证的方法。在本示例中，该类名为“Utils”。然后，需要添加以下 using 语句。

csharp

	using Microsoft.IdentityModel.Clients.ActiveDirectory;


接下来，请添加以下方法，以便从 Azure AD 检索 JWT 令牌。为了方便维护，请将硬编码的字符串值移到 Web 或应用程序配置。

csharp
	
	public async static Task<string> GetToken(string authority, string resource, string scope)
	{
	    var authContext = new AuthenticationContext(authority);
	
	    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");
	
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	
	    if (result == null)
	
	    throw new InvalidOperationException("Failed to obtain the JWT token");
	
	    return result.AccessToken;
	}


最后，可以添加所需的代码，以调用密钥保管库并检索机密值。首先，必须添加以下 using 语句。

csharp

	using Microsoft.Azure.KeyVault;


接下来，需要添加方法调用，以调用密钥保管库并检索你的机密。在此方法中，需要提供在前面步骤中保存的机密 URI。请注意如何使用以前创建的 Utils 类中的 GetToken 方法。
    
csharp
	
	var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
	
	var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
	

现在，当你运行应用程序时，应该会向 Azure Active Directory 进行身份验证，然后从 Azure 密钥保管库中检索机密值。

##使用 Azure 自动化进行密钥轮替

对于存储为 Azure 密钥保管库机密的值，可以使用多种选项实现其轮替策略。机密可以在执行手动过程期间轮替、使用 API 调用以编程方式轮替，或者通过自动化脚本来轮替。在本文中，我们将使用 Azure PowerShell 并结合 Azure 自动化来更改 Azure 存储帐户访问密钥，然后使用该新密钥来更新密钥保管库机密。

若要允许 Azure 自动化在密钥保管库中设置机密值，需要获取建立 Azure 自动化实例时所创建的名为“AzureRunAsConnection”的连接的客户端 ID。可以通过从 Azure 自动化实例选择“资产”来获取此 ID。在此处选择“连接”，然后选择“AzureRunAsConnection”服务主体。请记下“应用程序 ID”。

![Azure 自动化客户端 ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

在未关闭“资产”窗口的情况下，另请选择“模块”。在模块中选择“库”，然后搜索并“导入”以下每个模块的更新版本。

	Azure
	Azure.Storage	
	AzureRM.Profile
	AzureRM.KeyVault
	AzureRM.Automation
	AzureRM.Storage
	
> [AZURE.NOTE] 在编写本文时，只有下面共享的脚本才需要更新上述模块。如果你发现自动化作业失败，应确认已导入所有必要的模块及其依赖项。

检索 Azure 自动化连接的应用程序 ID 之后，需要让 Azure 密钥保管库知道此应用程序有权更新保管库中的机密。可以使用以下 PowerShell 命令来实现此目的。

powershell
	
	Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
	

接下来，选择 Azure 自动化实例下面的“Runbook”资源，然后选择“添加 Runbook”。选择“快速创建”。为 Runbook 命名，然后选择“PowerShell”作为 Runbook 类型。（可选）添加描述。最后，单击“创建”。

![创建 Runbook](./media/keyvault-keyrotation/Create_Runbook.png)  


在新 Runbook 的编辑器窗格中粘贴以下 PowerShell 脚本。

powershell

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
	New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
	$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName
	
	$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force
	
	$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue


在编辑器窗格中，可以选择“测试窗格”来测试脚本。一旦运行脚本且未出错，便可以选择“发布”选项，然后返回 Runbook 的配置窗格以应用 Runbook 的计划。

##密钥保管库审核管道

设置 Azure 密钥保管库时，可以打开审核功能，以收集有关对密钥保管库发出的访问请求的日志。这些日志存储在指定的 Azure 存储帐户中供你提取、监视和分析。下面演练的方案将利用 Azure Functions、Azure Logic Apps 和密钥保管库审核日志来创建管道，以便在与 Web 应用的应用 ID 不匹配的应用检索保管库中的机密时发送电子邮件。

首先，需要对密钥保管库启用日志记录。可以通过以下 PowerShell 命令来实现此目的（在[此处](/documentation/articles/key-vault-logging/)可以看到完整详细信息）：

powershell
	
	$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
	$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
	Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent



启用日志记录后，审核日志将开始收集到指定的存储帐户中。这些日志包含有关如何、何时和谁访问了密钥保管库的事件。

> [AZURE.NOTE] 最多在执行密钥保管库操作 10 分钟后，就能访问其日志记录信息。但大多数情况下不用等待这么长时间。

下一步是[创建 Azure 服务总线队列](/documentation/articles/service-bus-dotnet-get-started-with-queues/)。这是密钥保管库审核日志的推送位置。进入队列后，逻辑应用将提取日志并对其采取措施。创建服务总线的过程相对简单直接，下面是概要步骤：

1. 创建服务总线命名空间（如果要在本示例中使用现有的命名空间，请跳到步骤 2）。
2. 在门户中导航到“服务总线”，然后选择要在其中创建队列的命名空间。
3. 选择“新建”，然后选择“服务总线”->“队列”并输入所需的详细信息。
4. 通过选择命名空间并单击“连接信息”来获取服务总线连接信息。在下一个部分需要用到此信息。

接下来，[创建 Azure 函数](/documentation/articles/functions-create-first-azure-function/)以轮询存储帐户中的密钥保管库日志并选择新事件。这是一个按计划触发的函数。

创建 Azure 函数（在门户中选择“新建”->“Function App”）。在创建过程中，可以使用现有的托管方案，或创建新的计划。也可以选择动态托管。在[此处](/documentation/articles/functions-scale/)可以找到有关函数托管选项的详细信息。

创建 Azure 函数后，请导航到该函数，选择计时器函数和 C#，然后单击开始屏幕中的“创建”。

![Azure Functions 开始边栏选项卡](./media/keyvault-keyrotation/Azure_Functions_Start.png)

在“开发”选项卡上，将 run.csx 代码替换为以下内容：

csharp
	
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

> [AZURE.NOTE] 请务必替换上述代码中的变量，以指向密钥保管库日志所写入到的存储帐户、前面创建的服务总线，以及密钥保管库存储日志的特定路径。

该函数将选择密钥保管库日志所写入到的存储帐户中的最新日志文件，从该文件获取最新事件，并将这些事件推送到服务总线队列。由于单个文件可以包含多个事件（例如一整个小时的事件），因此我们创建了一个 _sync.txt_ 文件，函数也会在其中查找，以确定所选的最后一个事件的时间戳。这可以确保不会多次推送相同的事件。此 _sync.txt_ 文件只包含上次遇到的事件的时间戳。加载日志时，必须根据该时间戳将日志排序，以确保其顺序正确。

在此函数中，我们引用了 Azure Functions 中几个无法现成使用的附加库。为了添加这些库，需要 Azure Functions 来使用 nuget 提取它们。选择“查看文件”选项

![“查看文件”选项](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)  


添加包含以下内容的名为 _project.json_ 的新文件：

json
	
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

单击“保存”后，将触发 Azure Functions 下载所需的二进制文件。

切换到“集成”选项卡，然后为计时器参数提供要在函数中使用的有意义名称。在上面的代码中，计时器名称应为 _myTimer_。如下所示为计时器指定 [CRON 表达式](/documentation/articles/web-sites-create-web-jobs/#CreateScheduledCRON)：0 * * * * *，这会导致函数每分钟运行一次。

在同一个“集成”选项卡中，添加类型为“Azure Blob 存储”的输入。这会指向包含函数所查找的最后一个事件的时间戳的 _sync.txt_ 文件。将在函数中按参数名称提供此文件。在上面的代码中，Azure Blob 存储输入希望参数名称为 _inputBlob_。选择 _sync.txt_ 文件所在的存储帐户（可能是相同或不同的存储帐户中），然后在路径字段中以 {container-name}/path/to/sync.txt 格式提供文件所在位置的路径。

添加类型为“Azure Blob 存储”的输出。这也会指向刚刚在输入中定义的 _sync.txt_ 文件。函数将使用此文件来写入所查找的最后一个事件的时间戳。在上面的代码中，此参数的名称应为 _outputBlob_。

现在，函数已准备就绪。请务必切换回到“开发”选项卡并_保存_代码。检查输出窗口中是否有任何编译错误并相应地更正。如果代码可以编译，则现在应该正在运行，然后每隔一分钟检查密钥保管库日志，并将所有新事件推送到定义的服务总线队列。每次触发该函数时，你应该都会看到向日志窗口写入日志记录信息。

###Azure 逻辑应用

接下来，我们需要创建一个 Azure 逻辑应用，用于选择函数推送到服务总线队列的事件、分析内容，然后根据匹配的条件发送电子邮件。

转到“新建”->“逻辑应用”来[创建逻辑应用](/documentation/articles/app-service-logic-create-a-logic-app/)。

创建逻辑应用后，导航到该应用并选择“编辑”。在逻辑应用编辑器中，选择“服务总线队列”管理的 API，然后输入你的服务总线凭据以将其连接到队列。

![Azure 逻辑应用服务总线](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

接下来选择“添加条件”。在条件中，切换到“高级编辑器”并输入以下内容，然后将 APP\_ID 替换为 Web 应用的实际 APP\_ID：


@equals('<APP\_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])


如果传入事件中的 **appid** 属性（即服务总线消息的正文）不是应用程序的 **appid**，则此表达式基本上会返回 **false**。

现在，请在“否则不执行任何操作...”选项下面创建操作。

![在 Azure 逻辑应用中选择操作](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

对于操作，请选择“Office 365 - 发送电子邮件”。填写字段，创建当定义的条件返回 false 时要发送的电子邮件。如果你没有 Office 365，可以查看能够达到相同效果的替代方案。

现在，你已创建了端到端管道，它将每隔一分钟查找是否有新的密钥保管库审核日志。如果找到任何新日志，会将其发送到服务总线队列。新消息进入队列后，将触发逻辑应用，如果事件中的 appid 与调用方应用程序的应用 ID 不匹配，则发送电子邮件。

<!---HONumber=Mooncake_0829_2016-->