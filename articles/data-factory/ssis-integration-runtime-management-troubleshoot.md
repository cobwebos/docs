---
title: 排查 Azure 数据工厂中的 SSIS Integration Runtime 管理问题 |Microsoft Docs
description: 本文提供了有关 SSIS Integration Runtime (SSIS IR) 的管理问题的疑难解答指南
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 17fe8d9ed02156b8fe9aafd7adca946531895883
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253021"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>排查 Azure 数据工厂中的 SSIS Integration Runtime 管理问题

本文档提供有关 SSIS Integration Runtime (SSIS IR) 的管理问题的疑难解答指南。

## <a name="overview"></a>概述

如果预配或取消预配 SSIS IR 时出现任何问题, 则会在 ADF 门户中出现错误消息或从 PowerShell cmdlet 返回错误消息。 错误的格式始终为错误代码, 并提供详细的错误消息。

这意味着, 如果错误代码为 InternalServerError, 则服务会出现一些暂时性问题。 稍后可以重试该操作。 如果重试不起作用, 请联系 Azure 数据工厂支持团队。

有三个主要的外部依赖项可能会导致错误:如果错误代码不是 InternalServerError, 则 Azure SQL 数据库服务器或托管实例、自定义安装脚本和虚拟网络配置。

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL 数据库服务器或托管实例问题

如果预配 ssis 目录数据库的 SSIS IR, 则需要 Azure SQL 数据库服务器或托管实例。 SSIS IR 应可以访问 Azure SQL 数据库服务器或托管实例。 Azure SQL 数据库服务器或托管实例的帐户应具有创建 SSIS 目录数据库 (SSISDB) 的权限。 如果有任何错误, 则会在 ADF 门户中显示带有详细的 SQL 异常消息的错误代码。 请按照以下步骤进行错误代码的故障排除。

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

当你在运行中预配新的 SSIS IR 或期间, 你可能会看到此问题。

如果在 IR 预配过程中出现错误, 则可能是由于以下原因导致的, 并且可以在错误消息中获取详细的 SqlException 消息。

* 网络连接问题。 请检查 SQL Server 或托管实例主机名是否可访问, 并且没有防火墙或 NSG 阻止 SSIS IR 来访问服务器。
* 登录失败并使用 SQL 身份验证。 它表示提供的帐户无法登录到 SQL Server。 请确保提供了正确的用户帐户。
* 登录失败, 并使用 AAD 身份验证 (托管标识)。 将工厂的托管标识添加到 AAD 组, 并使托管标识具有对你的目录数据库服务器的访问权限。
* 连接超时值始终是由与安全相关的配置引起的。 建议创建新的 VM, 如果 IR 在 VNet 中, 则让 VM 加入相同的 IR VNet, 然后安装 SSMS 并检查 Azure SQL 数据库服务器或托管实例状态。

有关其他问题, 请参阅详细的 SQL 异常错误消息, 并修复错误消息中显示的问题。 如果仍有问题, 请联系 Azure SQL 数据库服务器或托管实例支持团队。

如果在运行 IR 时出现错误, 则可能会更改某些网络安全组或防火墙, 这会导致 SSIS IR 辅助角色节点无法访问 Azure SQL 数据库服务器或托管实例。 取消阻止 SSIS IR 辅助节点访问 Azure SQL 数据库服务器或托管实例。

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

错误消息类似于 "数据库 ' SSISDB ' 已达到其大小配额"。 分区或删除数据、删除索引或查阅文档以获取可能的解决方法。 " 可能的解决方案包括:
* 增加 SSISDB 的大小配额。
* 更改 SSISDB 的配置以减小大小, 例如:
   * 缩短保留期和项目版本的数量。
   * 减少日志的保留期。
   * 更改日志的默认级别, 等等。

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

此错误表示 Azure SQL 数据库服务器或托管实例已具有其他 IR 创建并使用的 SSISDB。 需要提供不同的 Azure SQL 数据库服务器或托管实例, 或者删除现有 SSISDB 并重新启动新的 IR。

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

此错误可能是由以下原因引起的:

* 为 SSIS IR 配置的用户帐户没有创建数据库的权限。 您可以向用户授予权限以创建数据库。
* 创建数据库超时, 如执行超时、数据库操作超时等。 可以稍后重试, 查看问题是否已解决。 如果重试不起作用, 请联系 Azure SQL 数据库服务器或托管实例支持团队。

对于其他问题, 请检查 SQL 异常错误消息, 并修复错误消息中提到的问题。 如果仍有问题, 请联系 Azure SQL 数据库服务器或托管实例支持团队。

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

错误消息类似于 "无效的对象名称 ' catalog_properties '。", 这意味着您已经有一个名为 SSISDB 的数据库, 但它不是由 SSIS IR 创建的, 或者该数据库处于无效状态, 该状态是由上一个 SSIS IR 预配中的错误引起的。 可以删除名称为 SSISDB 的现有数据库, 或为 IR 配置新的 Azure SQL 数据库服务器或托管实例。

## <a name="custom-setup"></a>自定义安装

自定义安装程序提供了一个接口, 用于在设置或重新配置 SSIS IR 时添加自己的设置步骤。 有关详细信息, 请参阅为[AZURE SSIS 集成运行时自定义安装程序](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

确保容器只包含必要的自定义安装文件, 因为容器中的所有文件都将下载到 SSIS IR 辅助节点上。 建议在本地计算机上测试自定义安装脚本, 以解决任何脚本执行问题, 然后在 SSIS IR 中运行该脚本。

在运行 IR 时, 将检查自定义安装脚本容器, 因为 SSIS IR 为定期更新, 需要再次访问容器以下载自定义安装脚本并再次安装。 该检查将包括容器是否可访问以及是否存在主 .cmd 文件。

使用自定义安装时出现的任何错误, 都将显示错误 "代码 CustomSetupScriptFailure", 请检查包含错误代码的错误消息。  按照以下步骤解决子错误代码。  

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

这意味着, 对于自定义安装, SSIS IR 无法访问 Azure blob 容器。 检查容器的 SAS URI 是否可访问且未过期。

如果 IR 处于 "正在运行" 状态, 请先停止 IR, 再用新的自定义安装容器 SAS URI 重新配置 IR, 然后再次启动 IR。

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

它表示 SSIS IR 在 blob 容器中找不到自定义安装脚本 (main .cmd)。 确保容器中存在 main .cmd, 这是自定义安装程序的入口点。

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

这意味着自定义安装脚本 (main .cmd) 的执行失败, 你可以先在本地计算机上尝试该脚本, 或者在 blob 容器中检查 "自定义安装执行日志"。

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

它表示执行自定义安装脚本超时。 确保 blob 容器只包含必要的自定义安装文件。 还可以检查 blob 容器中的自定义安装执行日志。 自定义安装程序的最长持续时间设置为45分钟, 超过该时间段后, 最长期限包括从容器下载所有文件并将其安装在 SSIS IR 上的时间。 如果需要更长的时间, 请提供一个支持票证。

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

这意味着将自定义安装程序执行日志上载到 blob 容器失败, 原因是 SSIS IR 没有对 blob 容器的写入权限, 或者某些存储或网络问题。 如果自定义安装成功, 此错误不会影响任何 SSIS 功能, 但会丢失日志。 如果自定义安装因其他错误而失败, 并且我们未能上传日志, 我们将首先报告此错误, 以便可以上载日志进行分析, 并在解决此问题后, 我们将报告更多指定的问题。 如果重试后未解决此问题, 请与 Azure 数据工厂支持团队联系。

## <a name="virtual-network-configuration"></a>虚拟网络配置

将 SSIS IR 加入虚拟网络 (VNet) 时, 它将使用用户订阅下的 VNet。 有关详细信息, 请参阅将[AZURE SSIS 集成运行时加入虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

存在 VNet 相关的问题时, 你将看到如下错误

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

这可能是由变体原因导致的。 按照以下步骤解决子错误代码。

### <a name="forbidden"></a>禁止

错误消息类似于 "subnetId 未为当前帐户启用。 Microsoft。批处理资源提供程序未在 VNet 的同一订阅下注册。

这意味着 Azure Batch 无法访问 VNet。 在 VNet 相同的订阅中注册 Microsoft Batch 资源提供程序。

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

错误消息类似于 "指定的 VNet 不存在, 或者 Batch 服务无权访问它" 或指定的子网 xxx 不存在。

这意味着 VNet 不存在或 Azure Batch 服务无法访问它, 或者提供的子网不存在。 请确保 VNet 和子网存在并且 Azure Batch 可以访问它们。

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

消息类似于 "未能在 Vnet 中设置 Integration Runtime。 如果配置了 DNS 服务器或 NSG 设置, 请确保 DNS 服务器可访问且 NSG 配置正确 "

DNS 服务器或 NSG 设置的某些自定义配置可能会导致无法解析或无法访问 SSIS IR 所需的 Azure 服务器名称。 有关详细信息, 请参阅[SSIS IR VNet 配置](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)文档。 如果仍有问题, 请与 Azure 数据工厂支持团队联系。

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR 将定期自动更新, 并在升级过程中创建新的 Azure Batch 池, 并将删除旧的 Azure Batch 池, 旧池的 VNet 相关资源将被删除, 并且将在你的订阅. 此错误表示由于在订阅或资源组级别删除锁定, 因此删除旧池的 VNet 相关资源失败。 帮助移除删除锁定。

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

SSIS IR 预配可能会因为某种原因而失败, 如果发生故障, 则会删除创建的所有资源。 但是, 由于在订阅或资源组级别存在资源删除锁定, 因此无法删除 VNet 资源。 删除删除锁定并重新启动 IR。

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

停止 SSIS IR 时, 将删除与 VNet 相关的所有资源, 但删除失败, 因为在订阅或资源组级别存在资源删除锁定。 帮助删除锁定并再次尝试停止。

### <a name="nodeunavailable"></a>NodeUnavailable

此错误发生在运行 IR 的过程中, 它表示 IR 在运行之前运行正常, 它始终是由于 DNS 服务器或 NSG 配置已更改而导致 SSIS IR 无法连接到依赖服务, 帮助解决 DNS 服务器或 NSG 配置问题有关详细信息, 请参阅[SSIS IR VNet 配置](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 如果仍有问题, 请与 Azure 数据工厂支持团队联系。
