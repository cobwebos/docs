---
title: Azure 托管 HSM 日志记录
description: 借助本教程开始使用托管 HSM 日志记录。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 22abd38ead1257b49eeae98acfcd74349f563811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90992070"
---
# <a name="managed-hsm-logging"></a>托管 HSM 日志记录 

在创建一个或多个托管 HSM 之后，可能需要监视 HSM 的访问方式、时间和访问者。 为此，可以启用日志记录，以便在提供的 Azure 存储帐户中保存信息。 系统会自动为指定的存储帐户创建名为 **insights-logs-auditevent** 的新容器。 可以使用此同一个存储帐户来收集多个托管 HSM 的日志。

最多在执行托管 HSM 操作 10 分钟后，就能访问其日志记录信息。 但大多数情况下不用等待这么长时间。  存储帐户中的日志完全由你管理：

* 请使用标准的 Azure 访问控制方法限制可访问日志的人员，以此保护日志。
* 删除不想继续保留在存储帐户中的日志。

借助本教程开始使用托管 HSM 日志记录。 你将创建一个存储帐户，启用日志记录，并解释收集的日志信息。  

> [!NOTE]
> 本教程不包含有关如何创建托管 HSM 或密钥的说明。 本文提供有关更新诊断日志记录的 Azure CLI 说明。

## <a name="prerequisites"></a>必备知识

若要完成本文中的步骤，必须准备好以下项：

* Microsoft Azure 订阅。 如果没有，可以注册[免费试用版](https://azure.microsoft.com/pricing/free-trial)。
* Azure CLI 版本 2.12.0 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。
* 订阅中的托管 HSM。 请参阅[快速入门：使用 Azure CLI 预配和激活托管 HSM](quick-create-cli.md)，预配和激活托管 HSM。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>连接到 Azure 订阅

设置密钥日志记录的第一步是将 Azure CLI 指向要记录的托管 HSM。

```azurecli-interactive
az login
```

若要详细了解通过 CLI 使用的登录选项，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

可能需要指定用于创建托管 HSM 的订阅。 输入以下命令以查看帐户的订阅：

## <a name="identify-the-managed-hsm-and-storage-account"></a>标识托管 HSM 和存储帐户

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>启用日志记录

若要启用托管 HSM 的日志记录，请使用“az monitor diagnostic-settings create”命令以及针对新存储帐户和托管 HSM 创建的变量。 还将“-Enabled”标志设置为“$true”，并将类别设置为“AuditEvent”（托管 HSM 日志记录的唯一类别）  ：

此输出确认托管 HSM 的日志记录现已启用，会将信息保存到存储帐户。

还可以选择性地为日志设置保留期策略，以便自动删除较旧的日志。 例如，通过将 **-RetentionEnabled** 标志设置为 **$true** 来设置保留期策略，并将 **-RetentionInDays** 参数设置为 **90**，以便自动删除 90 天以上的日志。

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

记录的内容：

* 所有已经过身份验证的 REST API 请求，包括由于访问权限、系统错误或错误请求而发生的失败请求。
* 对托管 HSM 本身的操作，包括创建、删除和更新属性（如标记）。
* 与安全域相关的操作，如初始化和下载、初始化恢复、上传
* 完整的 HSM 备份、还原和选择性还原操作
* 对密钥执行的操作，包括：
  * 创建、修改或删除密钥。
  * 对密钥进行签名、验证、加密、解密、包装和解包以及列出密钥。
  * 密钥备份、还原、清除
* 导致出现 401 响应的未经身份验证的请求。 例如，请求不包含持有者令牌、格式不正确或已过期，或者包含无效的令牌。  

## <a name="access-your-logs"></a>访问日志

托管 HSM 日志存储在提供的存储帐户的 insights-logs-auditevent 容器中。 若要查看这些日志，必须下载 Blob。 有关 Azure 存储的信息，请参阅[使用 Azure CLI 创建、下载和列出 Blob](../../storage/blobs/storage-quickstart-blobs-cli.md)。

单个 Blob 存储为文本，格式设置为 JSON。 让我们看一个示例日志项。 下面的示例显示了创建完整备份的请求发送到托管 HSM 时的日志条目。

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```

下表列出了字段的名称和描述：

| 字段名称 | 说明 |
| --- | --- |
| **TenantId** | 在其中创建托管 HSM 的订阅的 Azure Active Directory 租户 ID |
| **time** |日期和时间 (UTC)。 |
| **resourceId** |Azure 资源管理器资源 ID。 对于托管 HSM 日志而言，这始终是托管 HSM 资源 ID。 |
| **operationName** |下一份表格中所述操作的名称。 |
| **operationVersion** |客户端请求的 REST API 版本。 |
| **category** |结果的类型。 对于托管 HSM 日志而言，AuditEvent 是唯一可用的值。 |
| **resultType** |REST API 请求的结果。 |
| **properties** |此字段根据操作 (operationName) 包含不同的信息|
| **resultSignature** |HTTP 状态。 |
| **resultDescription** |有关结果的其他描述（如果有）。 |
| **durationMs** |为 REST API 请求提供服务所花费的时间，以毫秒为单位。 此时间不包括网络延迟，因此在客户端上测得的时间可能与此时间不匹配。 |
| **callerIpAddress** |发出请求的客户端的 IP 地址。 |
| **correlationId** |一个可选 GUID，客户端可传递此 GUID 来使客户端日志与服务端日志相关联。 |
| **identity** |在 REST API 请求中提供的令牌中的标识。 这通常是“用户”，一个“服务主体”。 |
| **requestUri** | REST API 请求 URI |
| **clientInfo** | 

## <a name="use-azure-monitor-logs"></a>使用 Azure Monitor 日志

可以使用 Azure Monitor 日志中的 Key Vault 解决方案查看托管 HSM AuditEvent 日志。 在 Azure Monitor 日志中，可以使用日志查询来分析数据并获取所需的信息。 

## <a name="next-steps"></a>后续步骤

- 了解预配和使用托管 HSM 的[最佳做法](best-practices.md)
- 了解[如何备份和还原](backup-restore.md)托管 HSM
