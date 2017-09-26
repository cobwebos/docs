---
title: "需要在 Azure 存储中安全传输 | Microsoft Docs"
description: "了解 Azure 存储的“需要安全传输”功能，以及如何启用它。"
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: 96c641672ce6515fad3abc3fc0b8a6af037de346
ms.contentlocale: zh-cn
ms.lasthandoff: 08/31/2017

---
# <a name="require-secure-transfer"></a>需要安全传输

“需要安全传输”选项通过仅允许从安全连接将请求发送到存储帐户可增强存储帐户的安全性。 例如，在调用 REST API 访问存储帐户时，必须使用 HTTPS 进行连接。 启用“需要安全传输”后，使用 HTTP 的任何请求将被拒绝。

使用 Azure 文件服务时，如果启用了“需要安全传输”，未加密的任何连接将失败。 这包括使用 SMB 2.1、未加密的 SMB 3.0 以及某些版本的 Linux SMB 客户端的方案。 

默认情况下，将禁用“需要安全传输”选项。

> [!NOTE]
> 由于 Azure 存储对自定义域名不支持 HTTPS，因此使用自定义域名时不应用此选项。

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>在 Azure 门户中启用“需要安全传输”

在 [Azure 门户](https://portal.azure.com)中创建存储帐户以及用于现有存储帐户时，都可以启用“需要安全传输”设置。

### <a name="require-secure-transfer-when-you-create-a-storage-account"></a>创建存储帐户时需要安全传输

1. 在 Azure 门户中打开“创建存储帐户”边栏选项卡。
1. 在“需要安全传输”下，选择“启用”。

  ![屏幕截图](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>对现有存储帐户需要安全传输

1. 在 Azure 门户中选择现有存储帐户。
1. 在“存储帐户”边栏选项卡菜单的“设置”下选择“配置”。
1. 在“需要安全传输”下，选择“启用”。

  ![屏幕截图](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>以编程方式启用“需要安全传输”

设置的名称为存储帐户属性中的 _supportsHttpsTrafficOnly_。 可通过 REST API、工具或库来启用它：

* [REST API](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts)（版本：2016-12-01）
* [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0)（版本：4.1.0）
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) （版本：2.0.11）
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/)（版本：1.1.0）
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview)（版本：6.3.0）
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0)（版本：1.1.0）
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)（版本： 0.11.0）

### <a name="enable-secure-transfer-required-setting-with-rest-api"></a>使用 REST API 启用"需要安全传输"设置

若要使用 REST API 简化测试，可以使用 [ArmClient](https://github.com/projectkudu/ARMClient) 从命令行调用。

 可以使用以下命令行检查 REST API 的设置：

```
# Login Azure and proceed with your credentials
> armclient login

> armclient GET  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01
```

在响应中，可以找到 _supportsHttpsTrafficOnly_ 设置。 示例：

```Json

{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}",
  "kind": "Storage",
  ...
  "properties": {
    ...
    "supportsHttpsTrafficOnly": false
  },
  "type": "Microsoft.Storage/storageAccounts"
}

```

可通过以下命令行，使用 REST API 启用设置：

```

# Login Azure and proceed with your credentials
> armclient login

> armclient PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01 < Input.json

```

Input.json 示例：
```Json

{
  "location": "westus",
  "properties": {
    "supportsHttpsTrafficOnly": true
  }
}

```

## <a name="next-steps"></a>后续步骤
Azure 存储提供一整套安全性功能，这些功能相辅相成，可让开发人员共同构建安全的应用程序。 有关详细信息，请访问[存储安全指南](storage-security-guide.md)。

