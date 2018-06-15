---
title: 更新 Azure 堆栈 SQL 资源提供程序 |Microsoft 文档
description: 了解如何更新 Azure 堆栈 SQL 资源提供程序。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 3a7656e54181c8e8e7b6b1bd39f80ce8ed01c807
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294854"
---
# <a name="update-the-sql-resource-provider"></a>更新 SQL 资源提供程序
更新 Azure 堆栈生成时，可能会发布新的 SQL 资源提供程序。 虽然现有的适配器可以继续使用，但仍建议尽快更新到最新的内部版本。 更新必须按顺序安装：不能跳过版本（请参见[部署资源提供程序的先决条件](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)中的版本列表）。

若要更新资源提供程序，请使用 *UpdateSQLProvider.ps1* 脚本。 此过程类似于用于安装资源提供程序的过程，如[部署资源提供程序](.\azure-stack-sql-resource-provider-deploy.md)一文中所述。 资源提供程序的下载包中提供此脚本。

*UpdateSQLProvider.ps1* 脚本可使用最新的资源提供程序代码创建新的 VM，并可将设置从旧 VM 迁移到新 VM。 迁移的设置包括数据库和宿主服务器信息，以及必需的 DNS 记录。

此脚本需要使用的参数正是针对 DeploySqlProvider.ps1 脚本进行描述的参数。 请同样在此处提供证书。 

建议从 Marketplace 管理下载最新的 Windows Server 2016 Core 映像。 如需安装更新，可以将单个 .MSU 包放置在本地依赖项路径中。 如果找到多个 .MSU 文件，脚本会发生故障。

下面是可从 PowerShell 提示符运行的 *UpdateSQLProvider.ps1* 脚本的示例。 请务必根据需要更改帐户信息和密码： 

> [!NOTE]
> 此更新过程仅适用于集成系统。

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 参数
可以在命令行中指定这些参数。 如果未指定参数或任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 同样必须将证书 .pfx 文件放在此目录中。 | _可选_（对于多节点部署是_必需_的） |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | （必需） |
| **MaxRetryCount** | 操作失败时，想要重试每个操作的次数。| 2 |
| **RetryDuration** |每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅下面的注释）。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |


## <a name="next-steps"></a>后续步骤

[维护 SQL 资源提供程序](azure-stack-sql-resource-provider-maintain.md)
