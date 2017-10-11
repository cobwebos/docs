---
title: "将拆分 / 合并服务部署 |Microsoft 文档"
description: "拆分和合并与弹性数据库工具"
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: 9a993c0f-7052-46cd-aa59-073bea8d535a
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 6e2fea882c248fa095a9d450ed54a7b4e64b45e1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-a-split-merge-service"></a>将拆分 / 合并服务部署
拆分 / 合并工具允许你分片数据库之间移动数据。 请参阅[向外扩展的云数据库之间移动数据](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>下载拆分 / 合并包
1. 下载最新的 NuGet 版本从[NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)。
2. 打开命令提示符并导航到您下载 nuget.exe 的目录。 下载内容还包括 PowerShell commmands。
3. 将最新的拆分 / 合并包下载到当前目录中使用以下命令：
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

文件放置在一个名为目录**Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x**其中*x.x.xxx.x*反映的版本号。 查找中的拆分 / 合并服务文件**content\splitmerge\service**子目录，拆分 / 合并 PowerShell 脚本 （和所需的客户端.dll） 中**content\splitmerge\powershell**子目录。

## <a name="prerequisites"></a>必备条件
1. 创建将用作拆分 / 合并状态数据库的 Azure SQL DB 数据库。 转到[Azure 门户](https://portal.azure.com)。 创建一个新**SQL 数据库**。 为数据库提供一个名称，创建一个新的管理员和密码。 请务必记录的名称和密码以供将来使用。
2. 确保你的 Azure SQL DB 服务器允许 Azure 服务连接到它。 在门户中，在**防火墙设置**，确保**允许访问 Azure 服务**设置设为**上**。 单击"保存"图标。
   
   ![允许的服务][1]
3. 创建 Azure 存储帐户将用于诊断输出。 转到 Azure 门户。 在左侧栏中，单击**新建**，单击**数据 + 存储**，然后**存储**。
4. 创建将包含拆分 / 合并服务的 Azure 云服务。  转到 Azure 门户。 在左侧栏中，单击**新建**，然后**计算**，**云服务**，和**创建**。 

## <a name="configure-your-split-merge-service"></a>配置你的拆分 / 合并服务
### <a name="split-merge-service-configuration"></a>拆分 / 合并服务配置
1. 在你下载拆分 / 合并程序集文件夹中，创建一份**ServiceConfiguration.Template.cscfg**文件**SplitMergeService.cspkg**和将其重命名**ServiceConfiguration.cscfg**。
2. 打开**ServiceConfiguration.cscfg**诸如 Visual Studio，用于验证证书指纹的格式如输入的文本编辑器中。
3. 创建新的数据库，或选择现有数据库以用作拆分 / 合并操作的状态数据库并检索该数据库的连接字符串。 
   
   > [!IMPORTANT]
   > 在此期间，状态数据库必须使用拉丁语排序规则 (SQL\_Latin1\_常规\_CP1\_CI\_AS)。 有关详细信息，请参阅[Windows 排序规则名称 (Transact SQL)](https://msdn.microsoft.com/library/ms188046.aspx)。
   >

   Azure SQL db，连接字符串通常是以下形式：
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. 在 cscfg 文件中输入此连接字符串**SplitMergeWeb**和**SplitMergeWorker**在 ElasticScaleMetadata 设置中的角色部分。
5. 有关**SplitMergeWorker**角色，到 Azure 存储中输入有效的连接字符串**WorkerRoleSynchronizationStorageAccountConnectionString**设置。

### <a name="configure-security"></a>配置安全性
有关配置服务安全性的详细说明，请参阅[拆分 / 合并安全配置](sql-database-elastic-scale-split-merge-security-configuration.md)。

对于本教程中的简单测试部署，配置步骤的最小集将执行即可服务启动并正在运行。 这些步骤启用仅一个计算机/帐户执行它们与服务进行通信。

### <a name="create-a-self-signed-certificate"></a>创建自签名证书
创建一个新目录，然后从该目录执行以下命令使用[Visual Studio 的开发人员命令提示](http://msdn.microsoft.com/library/ms229859.aspx)窗口：

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

将要求用于保护私钥的密码。 输入强密码并进行确认。 随后将提示你输入密码之后，使用。 单击**是**末尾将其导入到受信任根证书颁发机构存储区。

### <a name="create-a-pfx-file"></a>创建 PFX 文件
其中执行 makecert; 在同一窗口执行以下命令使用你用于创建证书的相同密码：

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>客户端证书导入到个人存储区
1. 在 Windows 资源管理器中，双击**MyCert.pfx**。
2. 在**证书导入向导**选择**当前用户**单击**下一步**。
3. 确认文件路径，然后单击**下一步**。
4. 键入密码、 保持**包括所有扩展的属性**检查并单击**下一步**。
5. 保留**自动选择证书存储 […]**检查并单击**下一步**。
6. 单击**完成**和**确定**。

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>将 PFX 文件上载到云服务
1. 转到[Azure 门户](https://portal.azure.com)。
2. 选择**云服务**。
3. 选择上面创建的拆分/合并服务的云服务。
4. 单击**证书**在顶部菜单中。
5. 单击**上载**底部栏中。
6. 选择 PFX 文件并按上面所述输入相同的密码。
7. 完成后，请从列表中的新条目复制证书指纹。

### <a name="update-the-service-configuration-file"></a>更新服务配置文件
粘贴之前复制到这些设置的指纹/值属性的证书指纹。
对于辅助角色：
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

对于 web 角色：

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

请注意，对于生产部署单独的证书应该用于用于加密、 服务器证书和客户端证书的 CA。 有关此方面的详细说明，请参阅[安全配置](sql-database-elastic-scale-split-merge-security-configuration.md)。

## <a name="deploy-your-service"></a>部署你的服务
1. 转到[Azure 门户](https://manage.windowsazure.com)。
2. 单击**云服务**在左侧选项卡并选择前面创建的云服务。
3. 单击**仪表板**。
4. 选择过渡环境，然后单击**上载新的过渡部署**。
   
   ![分步][3]
5. 在对话框中，输入部署标签。 对于程序包和配置中，单击从本地，然后选择**SplitMergeService.cspkg**文件和你之前配置的.cscfg 文件。
6. 确保复选框标记为**即使一个或多个角色包含单个实例部署**已选中。
7. 命中以开始部署右下角的勾选按钮。 它预计需要几分钟才能完成。

   ![上载][4]

## <a name="troubleshoot-the-deployment"></a>部署故障排除
如果你的 web 角色无法联机，它可能是安全配置出现了问题。 检查上文所述配置了 SSL。

如果辅助角色无法联机，但 web 角色已成功，但它很可能是连接到前面创建的状态数据库时出现问题。

* 请确保您的.cscfg 中的连接字符串准确。
* 服务器和数据库存在，检查和用户 id 和密码正确无误。
* 对于 Azure SQL DB，连接字符串应采用格式：

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* 确保服务器名称不以开头**https://**。
* 确保你的 Azure SQL DB 服务器允许 Azure 服务连接到它。 若要执行此操作，打开 https://manage.windowsazure.com、 在左侧单击"SQL 数据库"，在顶部，单击"服务器"和选择你的服务器。 单击**配置**顶部，并确保**Azure 服务**设置为"是"。 （请参阅这篇文章顶部的先决条件部分）。

## <a name="test-the-service-deployment"></a>测试服务部署
### <a name="connect-with-a-web-browser"></a>使用 web 浏览器连接
确定您的拆分 / 合并服务的 web 终结点。 你可以找到此 Azure 经典门户中通过转到**仪表板**你的云服务和下查找**站点 URL**右侧。 替换**http://**与**https://**由于默认安全设置将禁用 HTTP 终结点。 将此 url 将页加载到你的浏览器。

### <a name="test-with-powershell-scripts"></a>使用 PowerShell 脚本进行测试
可以通过运行包含的示例 PowerShell 脚本测试部署和您的环境。

包含的脚本文件是：

1. **SetupSampleSplitMergeEnvironment.ps1** -为拆分/合并设置测试数据层 （请参阅下表中的详细说明）
2. **ExecuteSampleSplitMerge.ps1** -执行测试操作对测试数据层 （请参阅下表中的详细说明）
3. **GetMappings.ps1** -顶级示例脚本可输出分片映射的当前状态。
4. **ShardManagement.psm1** -可包装 ShardManagement API 的帮助程序脚本
5. **SqlDatabaseHelpers.psm1** -用于创建和管理 SQL 数据库的帮助程序脚本
   
   <table style="width:100%">
     <tr>
       <th>PowerShell 文件</th>
       <th>步骤</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    创建分片映射管理器数据库</td>
     </tr>
     <tr>
       <td>2.    创建 2 个分片数据库。
     </tr>
     <tr>
       <td>3.    创建这些数据库 （删除这些数据库的任何现有的分片映射） 的分片映射。 </td>
     </tr>
     <tr>
       <td>4.    在这两个分片中创建小型示例表和填充的表中一个分片。</td>
     </tr>
     <tr>
       <td>5.    声明分片表的 SchemaInfo。</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell 文件</th>
       <th>步骤</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    将拆分请求发送到拆分 / 合并服务 web 前端，以将拆分半数据从第一个分片到第二个分片。</td>
     </tr>
     <tr>
       <td>2.    轮询拆分请求状态的 web 前端并等待该请求完成。</td>
     </tr>
     <tr>
       <td>3.    将合并请求发送到拆分 / 合并服务 web 前端，以将数据移从第二个分片返回到第一个分片。</td>
     </tr>
     <tr>
       <td>4.    轮询合并请求状态的 web 前端并等待该请求完成。</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>使用 PowerShell 来验证你的部署
1. 打开新的 PowerShell 窗口并导航到目录在此下载拆分 / 合并包，然后导航到"powershell"目录。
2. 创建 Azure SQL 数据库服务器 （或选择现有的服务器） 将在其中创建分片映射管理器和分片。
   
   > [!NOTE]
   > 默认情况下以简化脚本情况下，SetupSampleSplitMergeEnvironment.ps1 脚本将在同一台服务器上创建所有这些数据库。 这并不是限制的拆分 / 合并服务本身。
   >
   
   拆分 / 合并服务，才能移动数据并更新分片映射，将所需具有读/写访问权限的数据库的 SQL 身份验证登录名。 拆分 / 合并服务在云中运行，因为它当前不支持集成身份验证。
   
   请确保 Azure SQL 服务器配置为允许从运行这些脚本的计算机的 IP 地址进行访问。 你可以找到此设置下的 Azure SQL server / 配置 / 允许的 IP 地址。
3. 执行 SetupSampleSplitMergeEnvironment.ps1 脚本以创建示例环境。
   
   运行此脚本将擦除所有现有的分片映射管理数据在分片映射管理器数据库和分片上的结构。 它可能会有用重新运行该脚本，如果你想要重新初始化分片映射或分片。
   
   示例命令行：

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. 执行 Getmappings.ps1 脚本以查看示例环境中当前存在的映射。
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. 执行 ExecuteSampleSplitMerge.ps1 脚本以执行 split 操作 （将移动一半的数据在第一个分片到第二个分片），然后合并操作 （将数据移回到第一个分片）。 如果你配置了 SSL，并保留禁用 http 终结点，请确保改为使用 https:// 终结点。
   
   示例命令行：

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   如果你收到以下错误，则很可能你的 Web 终结点的证书问题。 请尝试连接到使用你最喜欢的 Web 浏览器的 Web 终结点，并检查是否有一个证书错误。
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   如果成功，输出应类似如下：
   
   ```
   > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
   > Sending split request
   > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
   > Polling split-merge request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > Sending merge request
   > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
   > Polling request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > 
   ```
6. 试用其他数据类型 ！ 所有这些脚本采用可选的-ShardKeyType 参数，它允许你指定的键类型。 默认值为 Int32，但你也可以指定 Int64、 Guid 或二进制文件。

## <a name="create-requests"></a>创建请求
通过使用 web UI 或通过导入并使用 SplitMerge.psm1 PowerShell 模块，其中将提交你的请求通过 web 角色，可以使用该服务。

服务可以将分片表和引用表中的数据移动。 分片表包含分片键列，并在每个分片上具有不同的行数据。 引用表不分片，因此它包含每个分片上的相同行数据。 引用表是适用于不经常更改，查询中的分片表中都使用联接到的数据。

为了执行拆分 / 合并操作，您必须声明分片表和你想要移动的引用表。 这实现的**SchemaInfo** API。 此 API 位于**Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**命名空间。

1. 对于每个分片表，创建**ShardedTableInfo**对象描述了表的父架构名称 (可选，默认为"dbo")，表名，并包含分片键的表中的列名称。
2. 对于每个引用表，创建**ReferenceTableInfo**对象描述了表的父架构名称 (可选，默认为"dbo") 和表名称。
3. 将上面的 TableInfo 对象添加到新**SchemaInfo**对象。
4. 获取对引用**ShardMapManager**对象，并调用**GetSchemaInfoCollection**。
5. 添加**SchemaInfo**到**SchemaInfoCollection**，从而提供分片映射名称。

SetupSampleSplitMergeEnvironment.ps1 脚本中，可以看到此示例。

拆分 / 合并服务不为你创建的目标数据库 （或数据库中的任何表的架构）。 它们向服务发送请求之前必须预先创建。

## <a name="troubleshooting"></a>疑难解答
你可能会看到以下消息时运行的示例 powershell 脚本：

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

此错误意味着你的 SSL 证书配置不正确。 请按照使用 web 浏览器的连接部分中的说明。

如果无法提交请求可能会看到此：

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

在这种情况下，请检查你的配置文件，尤其的设置**WorkerRoleSynchronizationStorageAccountConnectionString**。 此错误通常表示辅助角色无法成功初始化首次使用的元数据数据库。 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

