---
title: Data Lake Storage Gen1 Azure 的 PowerShell HDInsight 群集
description: 使用 Azure PowerShell 通过 Azure Data Lake Storage Gen1 创建和使用 Azure HDInsight 群集。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 2b4e5fad65d2ad358bca6b5a2b87d4aa36b77e73
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692075"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>通过 PowerShell 创建使用 Azure Data Lake Storage Gen1 作为默认存储的 HDInsight 群集

> [!div class="op_single_selector"]
> * [使用 Azure 门户](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell（对于默认存储）](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [使用 PowerShell（对于附加存储）](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用资源管理器](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

了解如何在 Azure PowerShell 中配置使用 Azure Data Lake Storage Gen1 作为默认存储的 Azure HDInsight 群集。 有关如何创建使用 Data Lake Storage Gen1 作为额外存储的 HDInsight 群集的说明，请参阅[创建使用 Data Lake Storage Gen1 作为额外存储的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-powershell.md)。

下面是结合使用 HDInsight 和 Data Lake Storage Gen1 的一些重要注意事项：

* HDInsight 版本 3.5 和 3.6 提供创建 HDInsight 群集（可访问作为默认存储的 Data Lake Storage Gen1）的选项。

* 创建 HDInsight 群集（可访问作为默认存储的 Data Lake Storage Gen1）的选项不可用于 HDInsight Premium 群集**。

若要通过 PowerShell 来配置可以使用 Data Lake Storage Gen1 的 HDInsight，请遵循后续五个部分中的说明。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在开始学习本教程之前，请确保满足以下要求：

* **一个 Azure 订阅**：转到[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更高版本**：参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。
* **Windows 软件开发工具包 (SDK)**：若要安装 Windows SDK，请转到[适用于 Windows 10 的下载内容和工具](https://dev.windows.com/downloads)。 该 SDK 用于创建安全证书。
* **Azure Active Directory 服务主体**：本教程介绍如何在 Azure Active Directory (Azure AD) 中创建服务主体。 但是，只有 Azure AD 管理员才能创建服务主体。 管理员可以跳过此先决条件部分，继续阅读本教程。

    >[!NOTE]
    >仅是 Azure AD 管理员时，才能创建服务主体。 Azure AD 管理员必须先创建服务主体，才能创建包含 Data Lake Storage Gen1 的 HDInsight 群集。 必须根据[使用证书创建服务主体](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)中所述，使用证书创建服务主体。
    >

## <a name="create-a-data-lake-storage-gen1-account"></a>创建 Data Lake Storage Gen1 帐户

若要创建 Data Lake Storage Gen1 帐户，请执行以下操作：

1. 在桌面上打开 PowerShell 窗口，并输入以下代码片段。 出现登录提示时，请以订阅管理员或所有者的身份登录。 

        # Sign in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > 如果在注册 Data Lake Storage Gen1 资源提供程序时收到类似于 `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` 的错误，原因可能是订阅未列入 Data Lake Storage Gen1 的允许列表。 要在 Data Lake Storage Gen1 中启用 Azure 订阅，请遵循[通过 Azure 门户开始使用 Data Lake Storage Gen1](data-lake-store-get-started-portal.md) 中的说明进行操作。
    >

2. Data Lake Store Gen1 帐户与 Azure 资源组关联。 首先请创建资源组。

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    应看到如下输出：

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. 创建 Data Lake Storage Gen1 帐户。 指定的帐户名只能包含小写字母和数字。

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
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

4. 要将 Data Lake Storage Gen1 用作默认存储，需要指定一个根路径，在创建群集过程中将复制此路径下的特定于群集的文件。 若要创建根路径（在代码片段中为“/clusters/hdiadlcluster”），请使用以下 cmdlet****：

        $myrootdir = "/"
        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>对 Data Lake Storage Gen1 设置基于角色访问的身份验证
每个 Azure 订阅都与一个 Azure AD 实体相关联。 使用 Azure 门户或 Azure 资源管理器 API 访问订阅资源的用户和服务时，首先必须使用 Azure AD 进行身份验证。 通过在 Azure 资源上为这些用户和服务分配相应角色，向其授予访问权限。 对于服务，服务主体用于标识 Azure AD 中的服务。

本部分说明如何向应用程序服务（例如 HDInsight）授予对 Azure 资源（前面创建的 Data Lake Storage Gen1 帐户）的访问权限。 为此，可为应用程序创建一个服务主体，并通过 PowerShell 向其分配角色。

若要为 Data Lake Storage Gen1 设置 Active Directory 身份验证，请执行以下两个部分中的任务。

### <a name="create-a-self-signed-certificate"></a>创建自签名证书
继续进行本部分中的步骤前，请确保已安装有 [Windows SDK](https://dev.windows.com/en-us/downloads)。 还必须事先创建一个目录例如 C:\mycertdir，会在该目录中创建证书**。

1. 在 PowerShell 窗口中，转到安装 Windows SDK 的位置（通常为 C:\Program Files (x86)\Windows Kits\10\bin\x86），并使用 [MakeCert][makecert] 实用工具创建一个自签名证书和私钥 **。 使用以下命令：

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    系统会提示输入私钥密码。 成功执行该命令后，指定的证书目录中应会出现 CertFile.cer 和 mykey.pvk********。
2. 使用 [Pvk2Pfx][pvk2pfx] 实用工具将 MakeCert 创建的.pvk 和.cer 文件转换为.pfx 文件。 运行以下命令：

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    出现提示时，请输入前面指定的私钥密码。 为 -po 参数指定的值是与 .pfx 文件关联的密码****。 成功完成该命令后，还应在指定的证书目录中看到**CertFile。**

### <a name="create-an-azure-ad-and-a-service-principal"></a>创建 Azure AD 和服务主体
本部分将为 Azure AD 应用程序创建一个服务主体，将角色分配给该服务主体，然后通过提供证书来以服务主体的身份进行身份验证。 若要在 Azure AD 中创建应用程序，请运行以下命令：

1. 将以下 cmdlet 粘贴到 PowerShell 控制台窗口中。 确保为 -DisplayName 属性指定的值是唯一的****。 -HomePage 和 -IdentiferUris 的值是占位符值，且未经验证********。

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
3. 授予服务主体访问之前指定的 Data Lake Storage Gen1 根路径和此路径中的所有文件夹的权限。 使用以下 cmdlet：

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>创建使用 Data Lake Storage Gen1 作为默认存储的 HDInsight Linux 群集

在本部分，我们将创建使用 Data Lake Storage Gen1 作为默认存储的 HDInsight Hadoop Linux 群集。 在此版本中，HDInsight 群集和 Data Lake Storage Gen1 必须位于同一位置。

1. 检索订阅租户 ID，并存储该 ID 供稍后使用。

        $tenantID = (Get-AzContext).Tenant.TenantId

2. 使用以下 cmdlet 创建 HDInsight 群集：

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStorageGen1Name                         # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster `
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
               -Version "3.6" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    成功完成该 cmdlet 后，应会出现列出群集详细信息的输出。

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>在 HDInsight 群集上运行测试作业以使用 Data Lake Storage Gen1
配置 HDInsight 群集后，可在该群集上运行测试作业，确保该群集可访问 Data Lake Storage Gen1。 为此，请运行示例 Hive 作业，以创建一个表，该表使用 Data Lake Storage Gen1 的* \<群集根>/example/data/sample.log*上已提供的示例数据。

在本部分，我们将与创建的 HDInsight Linux 群集建立安全外壳 (SSH) 连接，然后运行示例 Hive 查询。

* 如果使用 Windows 客户端来与群集建立 SSH 连接，请参阅[在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。
* 如果使用 Linux 客户端来与群集建立 SSH 连接，请参阅[在 Linux 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 建立连接后，请使用以下命令启动 Hive 命令行接口 (CLI)：

        hive
2. 使用该 CLI 输入以下语句，通过使用 Data Lake Storage Gen1 中的示例数据创建一个名为 vehicles 的新表****：

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    可以在 SSH 控制台上看到查询输出。

    >[!NOTE]
    >上面 CREATE TABLE 命令中的示例数据的路径为 `adl:///example/data/`，其中 `adl:///` 是群集根。 根据本教程中指定的群集根示例，该命令为 `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`。 可以使用更短的替代内容，或者提供群集根的完整路径。
    >

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>使用 HDFS 命令访问 Data Lake Storage Gen1
将 HDInsight 群集配置为使用 Data Lake Storage Gen1 后，可以使用 Hadoop 分布式文件系统 (HDFS) shell 命令来访问该存储。

在本部分，我们将与创建的 HDInsight Linux 群集建立 SSH 连接，然后运行 HDFS 命令。

* 如果使用 Windows 客户端来与群集建立 SSH 连接，请参阅[在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。
* 如果使用 Linux 客户端来与群集建立 SSH 连接，请参阅[在 Linux 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

建立连接后，使用以下 HDFS 文件系统命令列出 Data Lake Storage Gen1 中的文件。

    hdfs dfs -ls adl:///

可以使用 `hdfs dfs -put` 命令将一些文件上传到 Data Lake Storage Gen1，然后使用 `hdfs dfs -ls` 验证是否已成功上传这些文件。

## <a name="see-also"></a>另请参阅
* [将 Data Lake Storage Gen1 与 Azure HDInsight 群集配合使用](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Azure 门户：创建使用 Data Lake Storage Gen1 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
