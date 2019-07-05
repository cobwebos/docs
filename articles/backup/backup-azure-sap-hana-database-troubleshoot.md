---
title: 使用 Azure 备份的 SAP HANA 数据库在备份时解决错误 |Microsoft Docs
description: 本指南介绍如何排查尝试使用 Azure 备份的 SAP HANA 数据库备份时的常见错误。
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 33f1b4674aad35d55ab014c45cd73753533931cb
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514185"
---
# <a name="troubleshoot-back-up-of-sap-hana-server-on-azure"></a>在 Azure 上的 SAP HANA 服务器的备份故障排除

本文提供了用于保护 SAP HANA 数据库在 Azure 虚拟机的故障排除信息。 在继续之前故障排除，让我们来了解有关权限和设置的几个要点。

## <a name="understanding-pre-requisites"></a>了解系统必备组件

作为的一部分[先决条件](backup-azure-sap-hana-database.md#prerequisites)，预注册脚本应在运行虚拟机上安装 HANA 设置适当的权限的位置。

### <a name="setting-up-permissions"></a>设置权限

预注册脚本的作用：

1. 在 HANA 系统中创建 AZUREWLBACKUPHANAUSER 并将添加必需的角色和权限如下所示：
    - 数据库管理员-若要在还原过程中创建新数据库
    - 目录读取-读取备份目录
    - SAP_INTERNAL_HANA_SUPPORT – 若要访问几个专用表
2. 将密钥添加到为 HANA 插件 Hdbuserstore 来执行所有操作 （查询的数据库，配置备份、 执行备份、 还原）
   
   - 若要确认密钥的创建，请使用需要获取 SIDADM 凭据运行 HANA 机内 HDBSQL 命令：

    ``` hdbsql
    hdbuserstore list
    ```
    
    命令输出应显示为 AZUREWLBACKUPHANAUSER 与用户的密钥 {SID} {DBNAME}。

> [!NOTE]
> 请确保有一组唯一 SSFS 下的文件路径"/ usr/sap/{SID}/home/.hdb/"。 应只有一个此路径下的文件夹。

### <a name="setting-up-backint-parameters"></a>设置 BackInt 参数

一旦为备份选择数据库，Azure 备份服务将在数据库级别配置 backInt 参数。

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> 请确保这些参数都不存在主机级别。 主机级别的参数将重写这些参数，并可能会导致比预期的不同行为。

## <a name="understanding-common-user-errors"></a>了解常见用户错误

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 无法连接到你的系统已启动并正在运行的 HANA system.check。| Azure 备份服务不能连接到 HANA，因为 HANA DB 已关闭。 或 HANA 正在运行，但不是允许 Azure 备份服务连接 | 检查 HANA DB/服务是否按下。 如果 HANA DB/服务启动并运行，请检查是否所有权限都设置了如所述[此处](#setting-up-permissions)。 如果缺少该密钥，则重新运行该预注册脚本以创建新的密钥。 |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 检测到无效 Backint 配置。 停止保护并重新配置数据库。| Azure 备份错误地指定 backInt 参数。 | 请检查参数是否如所述[此处](#setting-up-backint-parameters)。 如果存在宿主中基于 backInt 参数，然后删除它们。 如果参数不在主机上存在但已被手动修改在数据库级别，然后将其转换为适当的值，如上所述。 或者停止保护并保留数据从 Azure 门户和恢复备份再一次。|
