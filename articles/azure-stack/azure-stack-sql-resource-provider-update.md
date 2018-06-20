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
ms.openlocfilehash: ac5073d1abc32b7598a869750f9c5a801559e9e6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264071"
---
# <a name="update-the-sql-resource-provider"></a>更新 SQL 资源提供程序

*适用于：Azure Stack 集成系统。*

当 Azure 堆栈更新到新的生成，可能会发布新的 SQL 资源提供程序。 虽然现有适配器继续工作，我们建议尽快更新到最新版本。

>[!IMPORTANT]
>必须在发布的顺序来安装更新。 不能跳过版本。 请参阅中的版本列表[部署资源提供程序系统必备组件](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)。

## <a name="overview"></a>概述

若要更新资源提供程序，使用*UpdateSQLProvider.ps1*脚本。 此脚本下载所随附的新的 SQL 资源提供程序。 更新过程是类似的过程用于[部署资源提供程序](.\azure-stack-sql-resource-provider-deploy.md)。 更新脚本使用的相同自变量为 DeploySqlProvider.ps1 脚本，并将需要提供证书信息。

### <a name="update-script-processes"></a>更新脚本进程

*UpdateSQLProvider.ps1*脚本使用最新的资源提供程序代码将创建新的虚拟机 (VM)。

>[!NOTE]
>建议从市场管理下载最新的 Windows Server 2016 Core 映像。 如果你需要安装更新，则可以放**单个**MSU 程序包在本地依赖关系路径。 如果在此位置没有多个 MSU 文件，则脚本将失败。

后*UpdateSQLProvider.ps1*脚本创建新的 VM，从旧的提供程序 VM 的脚本迁移以下设置：

* 数据库信息
* 托管服务器信息
* 所需的 DNS 记录

### <a name="update-script-powershell-example"></a>更新脚本的 PowerShell 示例

你可以编辑和从提升的 PowerShell ISE 中运行以下脚本。 请记得将更改的帐户信息和根据需要为你的环境的密码。

> [!NOTE]
> 此更新过程仅适用于 Azure 堆栈集成系统。

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
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
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 参数

运行脚本时，你可以指定以下参数从命令行。 如果不这样做，或者如果任何参数验证失败，系统会提示你提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure 堆栈服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **DependencyFilesLocalPath** | 此外必须在此目录中放置您的证书.pfx 文件。 | _可选对于单个节点，但对于多节点是必需的_ |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 你想要重试每个操作，如果失败的次数。| 2 |
| **RetryDuration** |每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |

## <a name="next-steps"></a>后续步骤

[维护 SQL 资源提供程序](azure-stack-sql-resource-provider-maintain.md)
