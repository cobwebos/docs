---
title: 带有 Data Lake Storage Gen1 附加存储的 PowerShell-HDInsight-Azure
description: 了解如何使用 Azure PowerShell 配置 Azure Data Lake Storage Gen1 为附加存储的 HDInsight 群集。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fb4ab1cdb60fff40effc1ff2f12f8600ba263d23
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692042"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>使用 Azure PowerShell 创建将 Azure Data Lake Storage Gen1 用作额外存储的 HDInsight 群集

> [!div class="op_single_selector"]
> * [使用门户](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell（对于默认存储）](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [使用 PowerShell（对于其他存储）](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用 Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

了解如何使用 Azure PowerShell 来配置包含 Azure Data Lake Storage Gen1（用作额外存储）的 HDInsight 群集****。 有关如何创建将 Data Lake Storage Gen1 用作默认存储的 HDInsight 群集的说明，请参阅[创建将 Data Lake Storage Gen1 用作默认存储的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)。

> [!NOTE]
> 若要将 Data Lake Storage Gen1 用作 HDInsight 群集的额外存储，强烈建议在创建群集时按本文说明进行操作。 向现有 HDInsight 群集添加 Data Lake Storage Gen1 作为额外存储是很复杂的过程，容易出现错误。
>

对于支持的群集类型，Data Lake Storage Gen1 可用作默认存储或额外存储帐户。 在 Data Lake Storage Gen1 用作额外存储时，该群集的默认存储帐户仍将是 Azure 存储 Blob (WASB)，与群集相关的文件（例如日志等）仍会写入到默认存储，而要处理的数据可以存储在 Data Lake Storage Gen1 帐户中。 使用 Data Lake Storage Gen1 作为额外存储帐户不会影响读/写到此群集的存储的性能或能力。

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>将 Data Lake Storage Gen1 用于 HDInsight 群集存储

下面是结合使用 HDInsight 和 Data Lake Storage Gen1 的一些重要注意事项：

* HDInsight 版本 3.2、3.4、3.5 和 3.6 提供创建 HDInsight 群集（可访问作为额外存储的 Data Lake Storage Gen1）的选项。

使用 PowerShell 配置 HDInsight 以与 Data Lake Storage Gen1 一起使用涉及以下步骤：

* 创建 Data Lake Storage Gen1 帐户
* 对 Data Lake Storage Gen1 设置基于角色访问的身份验证
* 创建具有 Data Lake Storage Gen1 身份验证的 HDInsight 群集
* 在此群集上运行作业

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在开始阅读本教程前，必须具有：

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更高版本**。 请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。
* **Windows SDK**。 可从[此处](https://dev.windows.com/en-us/downloads)进行安装。 可使用它来创建安全证书。
* **Azure Active Directory 服务主体**。 本教程中的步骤用于指导如何在 Azure AD 中创建服务主体。 但是，只有 Azure AD 管理员才能创建服务主体。 Azure AD 管理员可以跳过此先决条件，继续阅读本教程。

    **如果不是 Azure AD 管理员**，将无法执行创建服务主体所需的步骤。 在这种情况下，Azure AD 管理员必须先创建服务主体，然后才能创建包含 Data Lake Storage Gen1 的 HDInsight 群集。 此外，必须使用证书创建服务主体，如[使用证书创建服务主体](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)中所述。

## <a name="create-a-data-lake-storage-gen1-account"></a>创建 Data Lake Storage Gen1 帐户
按照这些步骤创建 Data Lake Storage Gen1 帐户。

1. 在桌面上，打开一个新的 Azure PowerShell 窗口，并输入下面的代码段。 当系统提示登录时，请确保以订阅管理员/所有者之一的身份登录：

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > 如果在注册 Data Lake Storage Gen1 资源提供程序时收到类似于 `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` 的错误，可能是未将订阅列入 Data Lake Storage Gen1 允许列表。 请按照以下[说明](data-lake-store-get-started-portal.md)，确保对 Data Lake Storage Gen1 启用 Azure 订阅。
   >
   >
2. Data Lake Store Gen1 帐户与 Azure 资源组关联。 首先创建 Azure 资源组。

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    应看到如下输出：

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. 创建 Data Lake Storage Gen1 帐户。 指定的帐户名称必须仅包含小写字母和数字。

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    应看到如下输出：

        ...
        ProvisioningState           : Succeeded
        State                       : Active
        CreationTime                : 5/5/2017 10:53:56 PM
        EncryptionState             : Enabled
        ...
        LastModifiedTime            : 5/5/2017 10:53:56 PM
        Endpoint                    : hdiadlstore.azuredatalakestore.net
        DefaultGroup                :
        Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
        Name                        : hdiadlstore
        Type                        : Microsoft.DataLakeStore/accounts
        Location                    : East US 2
        Tags                        : {}

5. 向 Data Lake Storage Gen1 上传一些示例数据。 本文后面将使用这些数据来验证是否可从 HDInsight 群集访问数据。 如果正在查找一些示例数据进行上传，可以从 **Azure Data Lake Git 存储库** 获取 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)文件夹。

        $myrootdir = "/"
        Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>对 Data Lake Storage Gen1 设置基于角色访问的身份验证

每个 Azure 订阅都会与 Azure Active Directory 关联。 使用 Azure 门户或 Azure 资源管理器 API 访问订阅资源的用户和服务必须首先进行 Azure Active Directory 身份验证。 通过在 Azure 资源上为这些用户和服务分配相应角色，向其授予访问权限。  对于服务，服务主体会识别 Azure Active Directory (AAD) 中的服务。 本部分介绍如何通过创建应用程序服务主体和通过 Azure PowerShell 向应用程序服务主体分配角色，向 HDInsight 等应用程序服务授予 Azure 资源（先前创建的 Data Lake Storage Gen1 帐户）访问权限。

若要设置 Data Lake Storage Gen1 的 Active Directory 身份验证，必须执行以下任务。

* 创建自签名证书
* 在 Azure Active Directory 中创建应用程序和服务主体

### <a name="create-a-self-signed-certificate"></a>创建自签名证书

继续进行本部分中的步骤前，请确保已安装有 [Windows SDK](https://dev.windows.com/en-us/downloads)。 还必须创建一个目录（该证书会在其中创建），例如 **C:\mycertdir**。

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

        $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. 使用应用程序 ID 创建服务主体。

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. 向将从 HDInsight 群集访问的 Data Lake Storage Gen1 文件夹和文件授予服务主体访问权限。 下面的代码片段提供对 Data Lake Storage Gen1 帐户（复制示例数据文件的位置）的根和文件自身的访问权限。

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>创建使用 Data Lake Storage Gen1 作为额外存储的 HDInsight Linux 群集

本部分创建将 Data Lake Storage Gen1 用作额外存储的 HDInsight Hadoop Linux 群集。 在此版本中，HDInsight 群集和 Data Lake Storage Gen1 帐户必须位于同一位置。

1. 首先检索订阅租户 ID。 之后需要此 ID。

        $tenantID = (Get-AzContext).Tenant.TenantId
2. 此版本中，对于 Hadoop 群集，Data Lake Storage Gen1 仅可用作此群集的额外存储。 默认存储仍是 Azure storage blobs (WASB)。 因此，首先来创建此群集所需的存储帐户和存储容器。

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

        New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzStorageContainer -Name $containerName -Context $destContext
3. 创建 HDInsight 群集。 使用以下 cmdlet。

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    此 cmdlet 成功完成后，应出现列出群集详细信息的输出。


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>在 HDInsight 群集上运行测试作业以使用 Data Lake Storage Gen1
配置 HDInsight 群集后，可在该群集上运行测试作业来测试该 HDInsight 群集是否可访问 Data Lake Storage Gen1。 为此，我们将运行示例 Hive 作业，该作业使用先前已上传至 Data Lake Storage Gen1 帐户的示例数据创建一个表。

本节中，以 SSH 方式连接到创建的 HDInsight Linux 群集中，然后运行示例 Hive 查询。

* 如果使用 Windows 客户端以 SSH 方式连接到群集中，请参阅[在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。
* 如果使用 Linux 客户端以 SSH 方式连接到群集中，请参阅[在 Linux 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. 连接后，请使用以下命令启动 Hive 命令行界面 (CLI)。

        hive
2. 使用该 CLI 输入以下语句，通过使用 Data Lake Storage Gen1 中的示例数据创建一个名为 vehicles 的新表****：

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    应该会看到与下面类似的输出：

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>使用 HDFS 命令访问 Data Lake Storage Gen1
将 HDInsight 群集配置为使用 Data Lake Storage Gen1 后，可使用 HDFS shell 命令访问此存储。

本节中，以 SSH 方式连接到创建的 HDInsight Linux 群集中，然后运行 HDFS 命令。

* 如果使用 Windows 客户端以 SSH 方式连接到群集中，请参阅[在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。
* 如果使用 Linux 客户端以 SSH 方式连接到群集中，请参阅[在 Linux 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

连接后，使用以下 HDFS 文件系统命令列出 Data Lake Storage Gen1 帐户中的文件。

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

这会列出先前上传到 Data Lake Storage Gen1 中的文件。

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

可以使用 `hdfs dfs -put` 命令将一些文件上传到 Data Lake Storage Gen1，然后使用 `hdfs dfs -ls` 验证是否已成功上传这些文件。

## <a name="see-also"></a>另请参阅
* [将 Data Lake Storage Gen1 与 Azure HDInsight 群集配合使用](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [门户：创建使用 Data Lake Storage Gen1 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
