---
title: "使用 Python 管理 Azure Data Lake Analytics | Microsoft Docs"
description: "了解如何使用 Python 创建 Data Lake Store 帐户和提交作业。 "
services: data-lake-analytics
documentationcenter: 
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: saveenr
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 0d69207c0b8bcbba6dee42a1dc856e9085629734
ms.contentlocale: zh-cn
ms.lasthandoff: 07/18/2017

---


# <a name="manage-azure-data-lake-analytics-using-python"></a>使用 Python 管理 Azure Data Lake Analytics

## <a name="python-versions"></a>Python 版本

* 使用 64 位版本的 Python。
* 可以使用位于 **[Python.org 下载](https://www.python.org/downloads/)**上的标准 Python 分发版。 
* 许多开发人员发现使用 **[Anaconda Python 分发版](https://www.continuum.io/downloads)**会十分方便。  
* 本文是使用来自标准 Python 分发版的 Python 版本 3.6 编写的

## <a name="install-azure-python-sdk"></a>安装 Azure Python SDK

安装以下模块：

* **azure-mgmt-resource** 模块包括 Active Directory 等的其他 Azure 模块。
* **azure-mgmt-datalake-store** 模块包括 Azure Data Lake Store 帐户管理操作。
* **azure-datalake-store** 模块包含 Azure Data Lake Store 文件系统操作。 
* **azure-datalake-analytics** 模块包含 Azure Data Lake Analytics 操作。 

首先，通过运行以下命令来确保具有最新 `pip`：

```
python -m pip install --upgrade pip
```

本文档是使用 `pip version 9.0.1` 编写的。

从命令行使用以下 `pip` 命令安装模块：

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>创建新 Python 脚本

将以下代码粘贴到脚本中：

```python
## Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

## Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

## Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

## Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

## Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

## Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInfo

## Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

## Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

## Use these as needed for your application
import logging, getpass, pprint, uuid, time
```

运行此脚本以验证是否可以导入模块。

## <a name="authentication"></a>身份验证

### <a name="interactive-user-authentication-with-a-pop-up"></a>使用弹出窗口的交互用户身份验证

不支持此方法。

### <a name="interactive-user-authentication-with-a-device-code"></a>使用设备代码的交互用户身份验证

```python
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>使用 SPI 和机密的非交互身份验证

```python
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>使用 API 和证书的非交互身份验证

不支持此方法。

## <a name="common-script-variables"></a>常见脚本变量

示例中使用了这些变量。

```python
subid= '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>' # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>创建客户端

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient( credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>创建 Azure 资源组

```python
armGroupResult = resourceClient.resource_groups.create_or_update( rg, ResourceGroup( location=location ) )
```

## <a name="create-data-lake-analytics-account"></a>创建 Data Lake Analytics 帐户

首先创建存储帐户。

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adls)]
    )
).wait()
```
然后创建使用该存储的 ADLA 帐户。

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>提交作业

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>等待作业结束

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print ('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>列出管道和重复周期
根据作业是附加了管道还是重复周期元数据，可以列出管道和重复周期。

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>管理计算策略

DataLakeAnalyticsAccountManagementClient 对象提供用于为 Data Lake Analytics 帐户管理计算策略的方法。

### <a name="list-compute-policies"></a>列出计算策略

下面的代码检索 Data Lake Analytics 帐户的计算策略列表。

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' + p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>创建新计算策略

下面的代码为 Data Lake Analytics 帐户创建新的计算策略，将对指定用户可用的最大 AU 设置为 50，并将最小作业优先级设置为 250。

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>后续步骤

- 若要了解使用其他工具来完成此教程，请单击页面顶部的选项卡选择器。
- 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)。
- 有关管理任务，请参阅 [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md)（使用 Azure 门户管理 Azure Data Lake Analytics）。


