---
title: "通过 Python 开始使用 Azure Data Lake Analytics | Microsoft 文档"
description: "了解如何使用 Python 创建 Data Lake Store 帐户和提交作业。 "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5618650671badfc54860c3ad8af5d1e727d3d8c9
ms.openlocfilehash: 40ccfc59cccd86a7634ec89656571b3cd23566b4
ms.lasthandoff: 11/23/2016


---


# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-python"></a>教程：通过 Python 开始使用 Azure Data Lake Analytics
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何使用 Python 创建 Azure Data Lake Analytics 帐户，在 [U-SQL](data-lake-analytics-u-sql-get-started.md) 中定义 Data Lake Analytics 作业，然后将作业提交到 Data Lake Analytics 帐户。 有关 Data Lake Analytics 的详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

本教程将开发一个作业，它能读取制表符分隔值 (TSV) 文件，并将其转换为逗号分隔值 (CSV) 文件。 若要通过其他支持的工具来完成此教程，请单击本节顶部的选项卡。

##<a name="prerequisites"></a>先决条件

开始学习本教程之前，必须做好以下准备：

- **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
- **一个 Azure Active Directory 应用程序**。 使用 Azure AD 应用程序对 Data Lake Store 应用程序进行 Azure AD 身份验证。 有不同的方式可进行 Azure AD 身份验证，即最终用户身份验证或服务到服务身份验证。 有关如何进行身份验证的说明和详细信息，请参阅[使用 Azure Active Directory 进行 Data Lake Store 身份验证](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。
- **[Python](https://www.python.org/downloads/)**。 必须使用 64 位版本。 本文使用 Python 版本 3.5.2。


## <a name="install-azure-python-sdk"></a>安装 Azure Python SDK

若要通过 Python 使用 Data Lake Store，需要安装三个模块。

azure-mgmt-datalake-store 模块包括 Azure Data Lake Store 帐户管理操作。 azure-mgmt-resource 模块包括 Active Directory 等的其他 Azure 模块。azure-datalake-store 模块包含 Azure Data Lake Store 文件系统操作。 azure-datalake-analytics 模块包含 Azure Data Lake Analytics 操作。 使用以下命令安装这些模块。

    pip install azure-mgmt-resource
    pip install azure-mgmt-datalake-store
    pip install azure-mgmt-datalake-analytics
    pip install azure-datalake-store

## <a name="create-a-python-application"></a>创建 Python 应用程序

1. 使用所选的 IDE 新建一个 Python 应用程序，例如 mysample.py。

2. 添加以下代码行导入所需的模块

        ## Use this only for Azure AD service-to-service authentication
        from azure.common.credentials import ServicePrincipalCredentials

        ## Use this only for Azure AD end-user authentication
        from azure.common.credentials import UserPassCredentials

        ## Required for Azure Resource Manager
        from azure.mgmt.resource.resources import ResourceManagementClient
        from azure.mgmt.resource.resources.models import ResourceGroup

        ## Required for Azure Data Lake Store account management
        from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
        from azure.mgmt.datalake.store.models import DataLakeStoreAccount

        ## Required for Azure Data Lake Store filesystem management
        from azure.datalake.store import core, lib, multithread

        ## Required for Azure Data Lake Analytics account management
        from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeAnalyticsAccountProperties, DataLakeStoreAccountInfo

        ## Required for Azure Data Lake Analytics job management
        from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
        from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

        ## Required for Azure Data Lake Analytics catalog management
        from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

        ## Use these as needed for your application
        import logging, getpass, pprint, uuid, time

3. 保存对 Python 应用程序文件所做的更改。

## <a name="authentication"></a>身份验证

使用以下方法之一进行身份验证：

### <a name="end-user-authentication-for-account-management"></a>为帐户管理使用最终用户身份验证

使用此方法可对 Azure AD 帐户管理操作（创建/删除 Data Lake Store 帐户，等等）进行身份验证。 必须提供 Azure AD 用户的用户名和密码。 不能为用户帐户配置多重身份验证，并且帐户不能是 Microsoft 帐户/Live ID，例如 @outlook.com, 和 @live.com.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>为帐户管理使用结合客户端机密的服务到服务身份验证

使用此方法可对 Azure AD 帐户管理操作（创建/删除 Data Lake Store 帐户，等等）进行身份验证。 可以使用以下代码片段通过应用程序/服务主体的客户端机密对应用程序进行非交互式身份验证。 请对现有的 Azure AD“Web 应用”应用程序使用这种身份验证。

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

## <a name="create-resource-management-group"></a>创建资源管理组

使用以下代码片段创建 Azure 资源组：

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'

    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Resource Group
    armGroupResult = resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )


## <a name="create-data-lake-store-account"></a>创建 Data Lake Store 帐户

每个 Data Lake Analytics 帐户都需要一个 Data Lake Store 帐户。 有关说明，请参阅 [Create a Data Lake Store account](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account)（创建 Data Lake Store 帐户）。



## <a name="create-data-lake-analytics-account"></a>创建 Data Lake Analytics 帐户

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'
    adlsAccountName = '<Azure Data Lake Store Account Name>'
    adlaAccountName = '<Azure Data Lake Analytics Account Name>'

    ## Create management client object
    adlaAcctClient = DataLakeAnalyticsAccountManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Data Lake Analytics account
    adlaAcctResult = adlaAcctClient.account.create(
        resourceGroup,
        adlaAccountName,
        DataLakeAnalyticsAccount(
            location=location,
            default_data_lake_store_account=adlsAccountName,
            data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adlsAccountName)]
        )
    ).wait()


##<a name="submit-data-lake-analytics-jobs"></a>提交 Data Lake Analytics 作业

Data Lake Analytics 作业使用 U-SQL 语言编写而成。 若要了解有关 U-SQL 的详细信息，请参阅 [U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)和 [U-SQL 语言参考](http://go.microsoft.com/fwlink/?LinkId=691348)。

    ## Declare variables
    adlsAccountName = '<Azure Data Lake Store Account Name>'

    ## Create management client object
    adlaJobClient = DataLakeAnalyticsJobManagementClient(
        credentials,
        'azuredatalakeanalytics.net'
    )

    ## Submit a U-SQL job
    jobId = str(uuid.uuid4())
    jobResult = adlaJobClient.job.create(
        adlaAccountName,
        jobId,
        JobInformation(
            name='Sample Job',
            type='USql',
            properties=USqlJobProperties(
                script='DROP DATABASE IF EXISTS FOO; CREATE DATABASE FOO;'
            )
        )
    )

    ## Print the job result
    while(jobResult.state != JobState.ended):
        print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
        time.sleep(3)
        jobResult = adlaJobClient.job.get(adlaAccountName, jobId)
        
    print ('Job finished with result: ' + jobResult.result.value)

## <a name="next-steps"></a>后续步骤

- 若要了解如何使用其他工具完成本教程，请单击页面顶部的选项卡选择器。
- 若要查看更复杂的查询，请参阅 [使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
- 若要着手开发 U-SQL 应用程序，请参阅 [使用 Data Lake Tools for Visual Studio 开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
- 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)。
- 有关管理任务，请参阅 [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md)（使用 Azure 门户管理 Azure Data Lake Analytics）。
- 有关 Data Lake Analytics 的概述，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。


