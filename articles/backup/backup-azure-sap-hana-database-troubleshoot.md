---
title: 排查 SAP HANA 数据库备份错误
description: 介绍如何对使用 Azure 备份来备份 SAP HANA 数据库时可能发生的常见错误进行故障排除。
ms.topic: conceptual
ms.date: 08/03/2019
ms.openlocfilehash: cbffa7415f315fd396e57afa355d2415c4612eb5
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172750"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>排查 Azure 上 SAP HANA 数据库的备份问题

本文提供了有关在 Azure 虚拟机上备份 SAP HANA 数据库的疑难解答信息。 以下部分介绍了诊断 SAP HANA 备份中常见错误时所需的重要概念数据。

## <a name="prerequisites"></a>先决条件

作为[先决条件](backup-azure-sap-hana-database.md#prerequisites)的一部分，请确保预先注册脚本已在安装了 HANA 的虚拟机上运行。

### <a name="setting-up-permissions"></a>设置权限

预先注册脚本的作用：

1. 在 HANA 系统中创建 AZUREWLBACKUPHANAUSER，并添加以下必需的角色和权限：
    - 数据库管理员：在还原期间创建新数据库。
    - 目录读取：读取备份目录。
    - SAP_INTERNAL_HANA_SUPPORT：访问一些私有表。
2. 为 HANA 插件添加用于处理所有操作（数据库查询、还原操作、配置和运行备份）的 Hdbuserstore 的键。

   若要确认密钥创建，请在 HANA 计算机上运行 HDBSQL 命令，并在其中包含 SIDADM 凭据：

    ``` hdbsql
    hdbuserstore list
    ```

    命令输出应显示 {SID} {DBNAME} 密钥，用户显示为 "AZUREWLBACKUPHANAUSER"。

> [!NOTE]
> 请确保在 **/usr/sap/{SID}/home/.hdb/** 下有一组唯一的 SSFS 文件。 此路径中应只有一个文件夹。

### <a name="setting-up-backint-parameters"></a>设置 BackInt 参数

选择用于备份的数据库后，Azure 备份服务将在数据库级别配置 backInt 参数：

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> 请确保这些参数*不*存在于主机级别。 主机级别参数将重写这些参数，并可能导致意外的行为。

## <a name="restore-checks"></a>还原检查

### <a name="single-container-database-sdc-restore"></a>单容器数据库（SDC）还原

在将 HANA 的单个容器数据库（SDC）还原到另一个 SDC 计算机时执行输入。 数据库名称应以小写形式提供，并在括号中追加 "sdc"。 HANA 实例将以大写字母显示。

假设已备份 SDC HANA 实例 "H21"。 "备份项" 页面会将备份项名称显示为 **"h21 （sdc）"** 。 如果尝试将此数据库还原到另一个目标 SDC （如 H11），则需要提供以下输入。

![SDC 还原输入](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

注意以下几点：

- 默认情况下，还原的数据库名称将用备份项名称（即 h21 （sdc））填充。
- 选择目标为 "H11" 不会自动更改还原的数据库名称。 **应将其编辑为 h11 （sdc）** 。 在 SDC 情况下，还原的数据库名称将是带有小写字母的目标实例 ID，并在括号中追加了 "SDC"。
- 由于 SDC 只能有一个数据库，因此还需要单击该复选框以允许使用恢复点数据覆盖现有的数据库数据。
- Linux 区分大小写，因此请务必保留大小写。

### <a name="multiple-container-database-mdc-restore"></a>多容器数据库（MDC）还原

在 HANA 的多个容器数据库中，标准配置为 SYSTEMDB + 1 个或更多租户数据库。 还原整个 SAP HANA 实例意味着还原 SYSTEMDB 和租户数据库。 首先还原 SYSTEMDB，然后继续进行租户数据库。 系统 DB 实质上是指覆盖所选目标上的系统信息。 这还会替代目标实例中的与 BackInt 相关的信息。 因此，将系统数据库还原到目标实例后，需要再次运行预注册脚本。 只有这样，随后的租户数据库还原才会成功。

## <a name="common-user-errors"></a>常见用户错误

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

数据| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 未能连接到 HANA 系统。 验证你的系统是否已启动并正在运行。| Azure 备份服务无法连接到 HANA，因为 HANA 数据库已关闭。 或 HANA 正在运行，但不允许 Azure 备份服务连接。 | 检查 HANA 数据库或服务是否已关闭。 如果 HANA 数据库或服务已启动并正在运行，请检查是否[已设置所有权限](#setting-up-permissions)。 如果缺少该密钥，请重新运行预先注册脚本来创建新密钥。 |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 检测到无效的 Backint 配置。 停止保护并重新配置数据库。| 错误地为 Azure 备份指定了 backInt 参数。 | 检查是否[已设置参数](#setting-up-backint-parameters)。 如果主机中存在基于 backInt 的参数，请将其删除。 如果在主机级别上没有参数，但在数据库级别手动修改了这些参数，则将它们还原到前面所述的相应值。 或者，运行 "**停止保护" 并保留**Azure 门户中的备份数据，然后选择 "**恢复备份**"。|
