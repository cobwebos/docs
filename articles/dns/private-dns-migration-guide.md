---
title: 将旧的 Azure DNS 专用区域迁移到新的资源模型
titleSuffix: Azure DNS
description: 本指南将分步说明如何将旧的 DNS 专用区域迁移到最新的资源模型
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: d29885104d6f39a17b5bdeb786cda8f56f58d987
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939348"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>将旧的 Azure DNS 专用区域迁移到新的资源模型

在公开预览期间，通过将“zoneType”属性设置为“Private”，使用“dnszones”资源创建专用 DNS 区域。 此类区域将在 2019 年 12 月 31 日之后不受支持，并且必须迁移到使用“privateDnsZones”资源类型而非“dnszones”的 GA 资源模型。 迁移过程很简单，我们提供了一个 PowerShell 脚本来自动执行此过程。 本指南将分步说明如何将 Azure DNS 专用区域迁移到新的资源模型。

若要查找需要迁移的 dnszones 资源，在 Azure CLI 中执行以下命令。
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>必备条件

确保已安装最新版 Azure PowerShell。 有关 Azure PowerShell (Az) 的详细信息及其安装方法，请访问 https://docs.microsoft.com/powershell/azure/new-azureps-module-az

确保已安装 Azure PowerShell 的 Az.PrivateDns 模块。 要安装此模块，打开提升的 PowerShell 窗口（管理模式），并输入以下命令

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>迁移过程完全自动化，并且预计不会导致停机。 但是，如果在关键生产环境中使用 Azure DNS 专用区域（预览版），应在计划内维护时段执行以下迁移过程。 在运行迁移脚本时，请确保不要修改 DNS 专用区域的配置或记录集。

## <a name="installing-the-script"></a>安装脚本

打开提升的 PowerShell 窗口（管理模式），并运行以下命令

```powershell
install-script PrivateDnsMigrationScript
```

当系统询问是否安装脚本时，输入 A

![安装脚本](./media/private-dns-migration-guide/install-migration-script.png)

你还可以在 https://www.powershellgallery.com/packages/PrivateDnsMigrationScript 网站上手动获取最新版 PowerShell 脚本

>[!IMPORTANT]
>迁移脚本不得在 Azure 云 shell 中运行，并且必须在连接到 Internet 的 VM 或本地计算机上执行。

## <a name="running-the-script"></a>运行脚本

执行以下命令以运行脚本

```powershell
PrivateDnsMigrationScript.ps1
```

![运行脚本](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>输入订阅 ID 并登录 Azure

系统将提示你输入包含希望迁移的 DNS 专用区域的订阅 ID。 可能会要求你登录 Azure 帐户。 完成登录，让脚本可以访问订阅中的 DNS 专用区域资源。

![登录到 Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>选择要迁移的 DNS 区域

使用脚本获取订阅中所有 DNS 专用区域的列表，并提示你确认希望迁移哪些区域。 输入 A 将迁移所有 DNS 专用区域。 一旦执行此步骤，脚本将使用新的资源模型创建新的 DNS 专用区域，并将数据复制到新的 DSN 区域。 无论如何，此步骤都不会更改现有 DNS 专用区域。

![选择 DNS 区域](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>将 DNS 解析切换到新的 DNS 区域

区域和记录复制到新的资源模型后，脚本会询问你是否将 DNS 解析切换到新的 DNS 区域。 此步骤将删除旧的 DNS 专用区域和你的虚拟网络之间的关联。 从虚拟网络取消链接旧区域时，上一步中创建的新 DNS 区域会自动接管这些虚拟网络的 DNS 解析。

选择 A 将切换所有虚拟网络的 DNS 解析。

![切换名称解析](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>验证 DNS 解析

继续操作前，请验证针对你的 DNS 区域的 DNS 解析是否按预期工作。 你可以登录 Azure VM 并对已迁移的区域发出 nslookup 查询，以验证 DNS 解析是否正常工作。

![验证名称解析](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

如果你发现 DNS 查询未解析，请等待几分钟，然后重试查询。 如果 DNS 查询按预期工作，请在脚本询问你是否从 DNS 专用区域中删除虚拟网络时输入 Y。

![确认名称解析](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>如果由于任何原因导致针对已迁移区域的 DNS 解析无法按预期工作，请在上一步中输入 N，脚本会将 DNS 解析切换回旧区域。 创建支持票证，我们可以帮助你迁移 DNS 区域。

## <a name="cleanup"></a>清理

此步骤将删除旧的 DNS 区域，并且只有在你验证了 DNS 解析按预期工作后，才应执行此步骤。 系统将询问你是否删除每个 DNS 专用区域。 在验证这些区域的 DNS 解析正常工作后，在每个提示符处输入 Y。

![清除](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>更新自动化

如果使用的自动化包括模板、PowerShell 脚本或使用 SDK 开发自定义代码，则必须更新自动化，以对 DNS 专用区域使用新的资源模型。 以下是新的 DNS 专用区域 CLI/PS/SDK 文档的链接。
* [Azure DNS 专用区域 REST API](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Azure DNS 专用区域 CLI](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Azure DNS 专用区域 PowerShell](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Azure DNS 专用区域 SDK](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>需要更多帮助

如果你在迁移过程中需要更多帮助或者由于任何原因上面列出的步骤不适合你，请创建支持票证。 请在支持票证中包括 PowerShell 脚本生成的脚本文件。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md) 在 Azure DNS 中创建专用区域。

* 了解可以通过 Azure DNS 中的专用区域实现的一些常见[专用区域方案](./private-dns-scenarios.md)。

* 有关 Azure DNS 专用区域的一些常见问题和解答，包括对于某些类型的操作可以期待的特定行为，请参阅[专用 DNS 常见问题解答](./dns-faq-private.md)。

* 访问 [DNS 区域和记录概述](dns-zones-records.md)，了解 DNS 区域和记录。

* 了解 Azure 的一些其他关键[网络功能](../networking/networking-overview.md)。
