---
title: "PowerShell：将 Data Lake Store 作为默认存储的 Azure HDInsight 群集 | Microsoft 文档"
description: "使用 Azure PowerShell 创建和使用包含 Azure Data Lake 的 HDInsight 群集"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/14/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 307070c755cff059b4b82494d28e06cf490a6f7a
ms.openlocfilehash: 6c5badbbea7385cac1407e4af148d5b647af04ac
ms.lasthandoff: 02/16/2017


---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-default-storage"></a>使用 Azure PowerShell 创建具有 Data Lake Store（作为默认存储）的 HDInsight 群集
> [!div class="op_single_selector"]
> * [使用门户](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell（对于默认存储）](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [使用 PowerShell（对于其他存储）](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用资源管理器](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

了解如何使用 Azure PowerShell 配置具有 Azure Data Lake Store（**作为默认存储**）的 HDInsight 群集。 有关如何创建具有作为附加存储的 Azure Data Lake Store 的 HDInsight 群集的说明，请参阅[创建具有作为附加存储的 Data Lake Store 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-powershell.md)。

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>将 Data Lake Store 用于 HDInsight 群集存储

下面是结合使用 HDInsight 和 Data Lake Store 的一些重要注意事项：

* HDInsight 版本 3.5 提供创建 HDInsight 群集（可访问作为默认存储的 Data Lake Store）的选项。

* 对于 HBase 群集（Windows 和 Linux），Data Lake Store **不支持**用作存储选项（包括默认存储和其他存储）。


使用 PowerShell 配置 HDInsight 来与 Data Lake Store 一起使用涉及以下步骤：

* 创建 Azure Data Lake Store
* 对 Data Lake Store 设置基于角色访问的身份验证
* 创建具有 Data Lake Store 身份验证的 HDInsight 群集
* 在此群集上运行作业

## <a name="prerequisites"></a>先决条件
在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更高版本**。 请参阅 [如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* **Windows SDK**。 可从[此处](https://dev.windows.com/en-us/downloads)进行安装。 可使用它来创建安全证书。
* **Azure Active Directory 服务主体**。 本教程中的步骤用于指导如何在 Azure AD 中创建服务主体。 但是，只有 Azure AD 管理员才能创建服务主体。 Azure AD 管理员可以跳过此先决条件，继续阅读本教程。

    **如果不是 Azure AD 管理员**，将无法执行创建服务主体所需的步骤。 在这种情况下，Azure AD 管理员必须先创建服务主体，然后才能创建包含 Data Lake Store 的 HDInsight 群集。 此外，必须使用证书创建服务主体，如[使用证书创建服务主体](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)中所述。

## <a name="create-an-azure-data-lake-store"></a>创建 Azure Data Lake Store
按照这些步骤创建 Data Lake Store。

1. 在桌面上，打开一个新的 Azure PowerShell 窗口，并输入下面的代码段。 当系统提示输入登录信息时，请确保以订阅管理员/所有者身份登录：

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > 注册 Data Lake Store 资源提供程序时如果收到类似于 `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` 的错误，则可能是因为未将订阅列为 Azure Data Lake Store 的白名单。 请按照以下[说明](data-lake-store-get-started-portal.md)，确保对 Data Lake Store 公共预览启用 Azure 订阅。
   >
   >
2. Azure Data Lake Store 帐户与 Azure 资源组是相关联的。 首先创建 Azure 资源组。

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![创建 Azure 资源组](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "创建 Azure 资源组")
3. 创建 Azure Data Lake Store 帐户。 指定的帐户名称必须仅包含小写字母和数字。

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![创建 Azure Data Lake 帐户](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "创建 Azure Data Lake 帐户")
4. 验证是否已成功创建帐户。

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    此输出应为 **True**。

5. 若要将 Data Lake Store 用作默认存储，需要指定一个根路径，在创建群集过程中将复制此路径下的特定于群集的文件。 使用下面的 cmdlet 创建根路径，在下面的代码段中此路径为 **/clusters/hdiadlcluster**。

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>对 Data Lake Store 设置基于角色访问的身份验证
每个 Azure 订阅都会与 Azure Active Directory 关联。 使用 Azure 经典门户或 Azure Resource Manager API 访问订阅资源的用户和服务必须首先进行 Azure Active Directory 身份验证。 通过在 Azure 资源上为这些用户和服务分配相应角色，向其授予访问权限。  对于服务，服务主体会识别 Azure Active Directory (AAD) 中的服务。 本部分介绍如何通过创建应用程序服务主体和通过 Azure PowerShell 向应用程序服务主体分配角色，向 HDInsight 等应用程序服务授予 Azure 资源（先前创建的 Azure Data Lake Store 帐户）访问权限。

若要设置 Azure Data Lake 的 Active Directory 身份验证，必须执行以下任务。

* 创建自签名证书
* 在 Azure Active Directory 中创建应用程序和服务主体

### <a name="create-a-self-signed-certificate"></a>创建自签名证书
继续进行本部分中的步骤前，请确保已安装有 [Windows SDK](https://dev.windows.com/en-us/downloads)。 还必须创建一个目录（该证书将在其中创建），例如 **C:\mycertdir**。

1. 在 PowerShell 窗口中，导航到安装 Windows SDK 的位置（通常为 `C:\Program Files (x86)\Windows Kits\10\bin\x86`），并使用 [MakeCert][makecert] 实用工具创建一个自签名证书和私钥。 使用以下命令。

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        
        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    系统会提示输入私钥密码。 此命令成功执行后，指定的证书目录中应会出现 **CertFile.cer** 和 **mykey.pvk**。
2. 使用 [Pvk2Pfx][pvk2pfx] 实用工具将 MakeCert 创建的.pvk 和.cer 文件转换为.pfx 文件。 运行以下命令。

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    出现提示时，输入先前指定的私钥密码。 为 **-po** 参数指定的值即是与此 .pfx 文件关联的密码。 此命令成功完成后，指定的证书目录中也应会出现 CertFile.pfx。

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>创建 Azure Active Directory 和服务主体
此部分中将执行以下用于创建 Azure Active Directory 应用程序的服务主体、将角色分配给服务主体，通过提供证书作为服务主体进行身份验证的步骤。 运行以下命令在 Azure Active Directory 中创建应用程序。

1. 将以下 cmdlet 粘贴到 PowerShell 控制台窗口中。 请确保为 **-DisplayName** 属性指定的值唯一。 此外，**-HomePage** 和 **-IdentiferUris** 的值是占位符值，且未进行验证。

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. 使用应用程序 ID 创建服务主体。

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. 授予服务主体访问之前指定的 Data Lake Store 根路径和此路径中的所有文件夹的权限。 使用下面的 cmdlet。

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-default-storage"></a>创建具有作为默认存储的 Data Lake Store 的 HDInsight Linux 群集

本节中将创建具有作为默认存储的 Data Lake Store 的 HDInsight Hadoop Linux 群集。 对于此版本，HDInsight 群集和 Data Lake Store 必须位于同一位置。

1. 首先检索订阅租户 ID。 之后需要此 ID。

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
        
2. 创建 HDInsight 群集。 使用以下 cmdlet。

        # Set these variables
        
        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                          # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.5" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    此 cmdlet 成功完成后，应出现列出群集详细信息的输出。

        
## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>在 HDInsight 群集上运行测试作业以使用 Data Lake Store
配置 HDInsight 群集后，可在该群集上运行测试作业来测试该 HDInsight 群集是否可访问 Data Lake Store。 为此，我们将运行示例 Hive 作业，该作业使用 Data Lake Store 的 **<cluster root>/example/data/sample.log** 中已提供的示例数据创建一个表。

本节中，将以 SSH 方式连接到创建的 HDInsight Linux 群集中，然后运行示例 Hive 查询。

* 如果使用 Windows 客户端以 SSH 方式连接到群集中，请参阅[在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。
* 如果使用 Linux 客户端以 SSH 方式连接到群集中，请参阅[在 Linux 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. 连接后，请使用以下命令启动 Hive 命令行界面 (CLI)。

        hive
2. 使用此 CLI，输入以下语句，通过使用 Data Lake Store 中的示例数据创建一个名为“车辆”的新表：

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    可以在 SSH 控制台上看到查询输出。 

    > [!NOTE]
       > 上面的 CREATE TABLE 命令中的示例数据的路径为 `adl:///example/data/`，其中 `adl:///` 是群集根。 本教程中指定的群集根的示例为 `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`。 因此，你可以使用较短的替代项，或者提供群集根的完整路径。 
       >
       >



## <a name="access-data-lake-store-using-hdfs-commands"></a>使用 HDFS 命令访问 Data Lake Store
配置 HDInsight 群集使用 Data Lake Store 后，可使用 HDFS shell 命令访问此存储。

本节中，将以 SSH 方式连接到创建的 HDInsight Linux 群集中，然后运行 HDFS 命令。 

* 如果使用 Windows 客户端以 SSH 方式连接到群集中，请参阅[在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。
* 如果使用 Linux 客户端以 SSH 方式连接到群集中，请参阅[在 Linux 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

连接后，使用以下 HDFS 文件系统命令列出 Data Lake Store 中的文件。

    hdfs dfs -ls adl:///

可使用 `hdfs dfs -put` 命令上传部分文件到 Data Lake Store 中，然后使用 `hdfs dfs -ls` 验证是否已成功上传这些文件。

## <a name="see-also"></a>另请参阅
* [门户：创建 HDInsight 群集以使用 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

