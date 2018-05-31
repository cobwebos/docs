---
title: 使用 Azure REST API 查看安全中心策略符合性 | Microsoft Docs
description: 了解如何使用 Azure REST API 查看当前对安全中心策略的符合性。
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 6c6764eec59633f0bdd0fa396c1581117a0c1e1d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077762"
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>使用 REST API 查看安全中心策略符合性

安全中心会根据你定义的安全策略定期验证 Azure 资源。 安全中心还提供 REST API，可供通过你自己的应用程序查看符合性；你可以直接查询服务或将 JSON 结果导入其他应用程序。 

在本文中，你将学习如何从与订阅关联的所有 Azure 资源检索一组现行建议。

若要检索一组现行建议，请执行以下操作：
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>生成请求  

`{subscription-id}` 参数是必需的，并且应该包含定义策略的 Azure 订阅的订阅 ID。 如果拥有多个订阅，请参阅[使用多个订阅](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions)。  

`api-version` 参数是必需的。 目前，仅 `api-version=2015-06-01-preview` 支持这些终结点。 

以下标头是必需的： 

|请求标头|说明|  
|--------------------|-----------------|  
|Content-Type：|必需。 设置为 `application/json`。|  
|Authorization：|必需。 设置为有效的 `Bearer` [访问令牌](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)。 |  

## <a name="response"></a>响应  

成功响应会返回状态代码 200（正常），其中包含用于保护 Azure 资源的建议任务的列表。

``` json
{  
  "value": [  
    {  
       "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
       "name": "{task_id}",
       "type": "Microsoft.Security/locations/{region}/tasks",
       "properties": {
       "state": "Active",
       "subState": "NA",
       "creationTimeUtc": "{create-time}",
       "lastStateChangeTimeUtc": "{last-state-change}",
       "securityTaskParameters": "{security-task-properties}"
    } 
  ]  
}  
```  

**value** 中的每一项都代表一个建议：

|响应属性|说明|
|----------------|----------|
|**state** | 指示建议是 `active` 还是 `resolved`。 |
|**creationTimeUtc** | 以 UTC 表示的日期和时间，显示创建建议的时间。 |
|**lastStateChangeUtc** | 以 UTV 表示的上次状态更改日期和时间（如有）。 |
|**securityTaskParameters** | 详细介绍建议；属性因基础建议而异。 |
||
  
有关当前支持的建议的信息，请参阅[实现安全建议](https://docs.microsoft.com/azure/security-center/security-center-recommendations)。

其他状态代码指示错误条件。 在这些情况下，响应对象包含解释请求失败原因的说明。

``` json
{  
  "value": [  
    {  
      "description": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="example-response"></a>示例响应  

``` json
{  
  "value": [  
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
            "name": "{task_id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "vmId": "/subscriptions/{subscription-id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "vmName": "{vm_name}",
                    "severity": "{severity}",
                    "isOsDiskEncrypted": {is_os_disk_encrypted},
                    "isDataDiskEncrypted": {is_data_disk_encrypted},
                    "name": "EncryptionOnVm",
                    "uniqueKey": "EncryptionOnVmTaskParameters_/subscriptions/{subscription-id}/resourceGroups/{resoource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "resourceId": "/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}"
                }
            }
        },
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{location}/tasks/{task-id}",
            "name": "{task-id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "serverName": "{sql-server-name}",
                    "serverId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}",
                    "name": "Enable auditing for the SQL server",
                    "uniqueKey": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}/auditingPolicies/Default",
                    "resourceId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}"
                }
            }
        }  ]  
}  
```  

此响应显示两个建议；列表中的每一项都对应于特定建议。 第一项建议加密 Linux 虚拟机上的存储，第二项建议启用对 SQL Server 的审计。

建议因启用的策略而异。 若要了解详细信息（包括当前可用的建议），请参阅[管理 Azure 安全中心的安全建议](https://docs.microsoft.com/azure/security-center/security-center-recommendations)。


## <a name="see-also"></a>另请参阅  
- [设置安全策略](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [Azure 安全资源提供程序 REST API](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Azure REST API 入门](https://docs.microsoft.com/rest/api/azure/)   
- [Azure 安全中心 PowerShell 模块](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
