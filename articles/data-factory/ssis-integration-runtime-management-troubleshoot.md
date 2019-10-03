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
ms.openlocfilehash: 8abffdf443e26c03c38c12a3947a47a94157c9da
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609619"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>排查 Azure 数据工厂中的 SSIS Integration Runtime 管理问题

本文提供 Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) (也称为 SSIS IR) 中管理问题的疑难解答指导。

## <a name="overview"></a>概述

如果在预配或取消预配 SSIS IR 时遇到任何问题, 则会在 Microsoft Azure 数据工厂门户中看到一条错误消息或从 PowerShell cmdlet 返回错误消息。 错误始终显示为错误代码的格式, 并显示详细的错误消息。

如果错误代码为 InternalServerError, 则服务存在暂时性问题, 应稍后重试该操作。 如果重试不起作用, 请联系 Azure 数据工厂支持团队。

否则, 三个主要的外部依赖项可能会导致错误: Azure SQL 数据库服务器、托管实例、自定义安装脚本以及虚拟网络配置。

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL 数据库服务器或托管实例问题

如果要使用 SSIS 目录数据库预配 SSIS IR, 则需要 Azure SQL 数据库服务器或托管实例。 SSIS IR 必须能够访问 Azure SQL 数据库服务器或托管实例。 此外, Azure SQL 数据库服务器或托管实例的帐户应具有创建 SSIS 目录数据库 (SSISDB) 的权限。 如果出现错误, 则会在数据工厂门户中显示带有详细 SQL 异常消息的错误代码。 使用以下列表中的信息来对错误代码进行故障排除。

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

当你在预配新的 SSIS IR 或 IR 正在运行时, 可能会出现此问题。 如果在 IR 预配过程中遇到此错误, 则可能会在错误消息中获取详细的 SqlException 消息, 指示以下问题之一:

* 网络连接问题。 检查 SQL Server 或托管实例主机名是否可访问。 同时, 验证没有防火墙或网络安全组 (NSG) 阻止对服务器的 SSIS IR 访问。
* SQL 身份验证期间登录失败。 提供的帐户无法登录到 SQL Server 数据库。 请确保提供正确的用户帐户。
* Microsoft Azure Active Directory (Azure AD) 身份验证 (托管标识) 期间登录失败。 将工厂的托管标识添加到 AAD 组, 并确保托管标识具有对你的目录数据库服务器的访问权限。
* 连接超时。 此错误始终是由与安全相关的配置引起的。 建议：
  1. 创建新的 VM。
  1. 如果 IR 在虚拟网络中, 请将 VM 加入相同的 IR Microsoft Azure 虚拟网络。
  1. 安装 SSMS 并检查 Azure SQL 数据库服务器或托管实例状态。

对于其他问题, 请修复详细的 SQL 异常错误消息中显示的问题。 如果仍有问题, 请联系 Azure SQL 数据库服务器或托管实例支持团队。

如果在 IR 正在运行时出现错误, 则网络安全组或防火墙更改可能会阻止 SSIS IR 辅助角色节点访问 Azure SQL 数据库服务器或托管实例。 取消阻止 SSIS IR 辅助角色节点, 使其可以访问 Azure SQL 数据库服务器或托管实例。

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

这种错误消息可能如下所示:"数据库" SSISDB "已达到其大小配额。 分区或删除数据、删除索引或查阅文档以获取可能的解决方法。 " 

可能的解决方案包括:
* 增加 SSISDB 的配额大小。
* 更改 SSISDB 的配置以减小大小:
   * 缩短保留期和项目版本的数量。
   * 减少日志的保留期。
   * 更改日志的默认级别。

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

此错误表示 Azure SQL 数据库服务器或托管实例已具有 SSISDB, 并且它正被另一个 IR 使用。 需要提供不同的 Azure SQL 数据库服务器或托管实例, 或者删除现有 SSISDB 并重新启动新的 IR。

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

此错误可能是由以下原因之一导致的:

* 为 SSIS IR 配置的用户帐户没有创建数据库的权限。 您可以授予用户创建数据库的权限。
* 在数据库创建期间发生超时, 如执行超时或数据库操作超时。 你应稍后重试该操作。 如果重试不起作用, 请联系 Azure SQL 数据库服务器或托管实例支持团队。

对于其他问题, 请检查 SQL 异常错误消息, 并修复错误详细信息中提到的问题。 如果仍有问题, 请联系 Azure SQL 数据库服务器或托管实例支持团队。

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

这种错误消息如下所示:"无效的对象名称 ' catalog_properties '。"在这种情况下, 你已经有一个名为 SSISDB 的数据库, 但它不是由 SSIS IR 创建的, 或者该数据库处于无效状态, 这是由上一个 SSIS IR 预配中的错误引起的。 可以删除名称为 SSISDB 的现有数据库, 也可以为 IR 配置新的 Azure SQL 数据库服务器或托管实例。

## <a name="custom-setup-issues"></a>自定义安装问题

自定义安装程序提供了一个接口, 用于在设置或重新配置 SSIS IR 时添加自己的设置步骤。 有关详细信息, 请参阅为[AZURE SSIS Integration Runtime 自定义安装程序](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

确保容器只包含必需的自定义安装文件;容器中的所有文件都将下载到 SSIS IR 辅助角色节点。 建议在本地计算机上测试自定义安装脚本, 以解决任何脚本执行问题, 然后在 SSIS IR 中运行该脚本。

当 IR 正在运行时, 将检查自定义安装脚本容器, 因为 SSIS IR 会定期更新。 此更新需要访问容器以下载自定义安装脚本并再次安装。 此过程还会检查容器是否可访问, 以及主 .cmd 文件是否存在。

对于涉及自定义安装的任何错误, 您将看到 CustomSetupScriptFailure 错误代码和子代码, 如 CustomSetupScriptBlobContainerInaccessible 或 CustomSetupScriptNotFound。

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

此错误表示 SSIS IR 无法访问用于自定义安装的 Azure blob 容器。 确保容器的 SAS URI 可访问且未过期。

如果 IR 正在运行, 请停止它, 用新的自定义安装容器 SAS URI 重新配置 IR, 然后重启 IR。

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

此错误表示 SSIS IR 在 blob 容器中找不到自定义安装脚本 (main .cmd)。 请确保在容器中存在 main .cmd, 这是自定义安装程序的入口点。

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

此错误表示执行自定义安装脚本 (main .cmd) 失败。 首先在本地计算机上尝试脚本, 或检查 blob 容器上的自定义安装执行日志。

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

此错误表示执行自定义安装程序脚本超时。 请确保 blob 容器只包含必要的自定义安装程序文件。 还应检查 blob 容器中的自定义安装执行日志。 自定义安装程序的最长持续时间为45分钟, 超过此时间后, 最长期限包括从容器下载所有文件并将其安装在 SSIS IR 上的时间。 如果需要更长时间, 请提供支持票证。

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

此错误表示尝试将自定义安装程序执行日志上载到 blob 容器失败。 之所以出现此问题, 是因为 SSIS IR 对 blob 容器没有写入权限, 也可能是由于存储或网络问题造成的。 如果自定义安装成功, 此错误不会影响任何 SSIS 功能, 但日志将丢失。 如果自定义安装程序失败并出现另一个错误, 并且未上传日志, 我们将首先报告此错误, 以便上载日志以便进行分析。 另外, 在解决此问题后, 我们将报告更具体的问题。 如果重试后未解决此问题, 请与 Azure 数据工厂支持团队联系。

## <a name="virtual-network-configuration"></a>虚拟网络配置

将 SSIS IR 加入到 Azure 虚拟网络时, SSIS IR 将使用用户订阅下的虚拟网络。 有关详细信息, 请参阅将[AZURE SSIS Integration Runtime 加入虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

存在与虚拟网络相关的问题时, 你将看到以下错误之一。

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

发生此错误的原因有多种。 若要解决此问题, 请参阅[禁止](#forbidden)的、 [InvalidPropertyValue](#invalidpropertyvalue)和[MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings)部分。

### <a name="forbidden"></a>不可用

这种错误可能类似于:"SubnetId 没有为当前帐户启用。 Microsoft。批处理资源提供程序未在 VNet 的同一订阅下注册。

这些详细信息意味着 Azure Batch 不能访问虚拟网络。 在与虚拟网络相同的订阅下注册 Microsoft Batch 资源提供程序。

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

这种错误可能类似于以下形式之一: 

- "指定的 VNet 不存在, 或者 Batch 服务无权访问它。"
- "指定的子网 xxx 不存在。"

这些错误表示虚拟网络不存在, Azure Batch 服务无法访问该网络, 或者提供的子网不存在。 请确保该虚拟网络和子网存在并且 Azure Batch 可以访问它们。

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

这种错误消息可能如下所示:"无法在 VNet 中设置 Integration Runtime。 如果配置了 DNS 服务器或 NSG 设置, 请确保 DNS 服务器可访问且 NSG 配置正确。 "

在这种情况下, 你可能具有 DNS 服务器或 NSG 设置的自定义配置, 这会阻止解决或访问 SSIS IR 所需的 Azure 服务器名称。 有关详细信息, 请参阅[SSIS IR 虚拟网络配置](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 如果仍有问题, 请联系 Azure 数据工厂支持团队。

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR 会定期自动更新。 在升级过程中将创建一个新的 Azure Batch 池, 并删除旧的 Azure Batch 池。 此外, 还将删除旧池的与虚拟网络相关的资源, 并在订阅下创建新的与虚拟网络相关的资源。 此错误表示由于订阅或资源组级别的删除锁定, 删除旧池的虚拟网络相关资源失败。 由于客户控制和设置删除锁定, 因此在这种情况下, 它们必须删除删除锁定。

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

如果 SSIS IR 设置失败, 则会删除创建的所有资源。 但是, 如果订阅或资源组级别存在资源删除锁定, 则不会按预期方式删除虚拟网络资源。 若要修复此错误, 请删除删除锁定并重新启动 IR。

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

停止 SSIS IR 时, 将删除与虚拟网络相关的所有资源。 但如果在订阅或资源组级别存在资源删除锁定, 删除可能会失败。 在这里, 客户还将控制和设置删除锁定。 因此, 它们必须删除删除锁定, 然后再次停止 SSIS IR。

### <a name="nodeunavailable"></a>NodeUnavailable

当 IR 正在运行时, 会出现此错误, 这意味着 IR 已变得不正常。 此错误始终是由阻止 SSIS IR 连接到必要服务的 DNS 服务器或 NSG 配置引起的。 由于 DNS 服务器和 NSG 的配置是由客户控制的, 因此客户必须解决他们端的阻碍性问题。 有关详细信息, 请参阅[SSIS IR 虚拟网络配置](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 如果仍有问题, 请联系 Azure 数据工厂支持团队。
