---
title: SQL Server 版本的就地更改
description: 了解如何在 Azure 中更改 SQL Server 虚拟机的版本。
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: a57a432a5f0f8e5a6bd802ec08b18350da3a77b3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91293367"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Azure VM 上 SQL Server 版本的就地更改

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何在 Microsoft Azure 中更改 Windows 虚拟机 (VM) 上的 Microsoft SQL Server 版本。

## <a name="prerequisites"></a>先决条件

若要对 SQL Server 进行就地升级，请遵循以下条件：

- 需要 SQL Server 所需版本的安装介质。 如果客户有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)，则可以从[批量许可中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)获取其安装介质。 没有软件保障的客户可以使用 Azure Marketplace SQL Server VM 映像，该映像的 SQL Server 的更高版本 (通常位于 C:\SQLServerFull) 。
- 版本升级应遵循 [支持升级路径](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15?view=sql-server-ver15)。

## <a name="planning-for-version-change"></a>规划版本更改

建议你在执行版本更改之前查看以下各项：

1. 检查你计划升级到的版本中的新增功能：

   - [SQL 2019](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-ver15?view=sql-server-ver15)中的新增功能
   - [SQL 2017](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2017?view=sql-server-ver15)中的新增功能
   - [SQL 2016](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2016?view=sql-server-ver15)中的新增功能
   - [SQL 2014](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2016?view=sql-server-2014)中的新增功能

1. 建议你检查要更改的版本的 [兼容性证书](https://docs.microsoft.com/sql/database-engine/install-windows/compatibility-certification?view=sql-server-ver15) ，以便可以使用数据库兼容性模式来最大程度地降低升级的影响。
1. 您可以查看以下文章，以帮助确保成功的结果：

   - [视频：现代化 SQL Server |Pam Lahoud & Pedro Lopes |20年通过](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [用于 AB 测试的数据库实验助手](https://docs.microsoft.com/sql/dea/database-experimentation-assistant-overview?view=sql-server-ver15)
   - [使用查询优化助手升级数据库](https://docs.microsoft.com/sql/relational-databases/performance/upgrade-dbcompat-using-qta?view=sql-server-ver15)
   - [更改数据库兼容性级别和使用查询存储](https://docs.microsoft.com/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store?view=sql-server-ver15)

## <a name="upgrade-sql-version"></a>升级 SQL 版本

> [!WARNING]
> 升级版本的 SQL Server 除了重启任何关联的服务（如 Analysis Services 和 R Services）以外，还将重新启动该 SQL Server 服务。

若要升级 SQL Server 的版本，请获取 [支持 SQL Server 升级路径](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15?view=sql-server-ver15) 的更高版本的 SQL Server 安装媒体，然后执行以下步骤：

1. 在开始此过程之前，请备份数据库，包括除 tempdb) 和用户数据库外的系统 (。 你还可以使用 Azure 备份服务创建与应用程序一致的 VM 级别备份。
1. 从 SQL Server 安装媒体开始 Setup.exe。
1. 安装向导将启动 SQL Server 安装中心。 若要升级 SQL Server 的现有实例，请在导航窗格中选择 " **安装** "，然后选择 " **从早期版本的 SQL Server 升级**"。

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="用于升级 SQL Server 版本的选择":::

1. 在 " **产品密钥** " 页上，选择一个选项以指示您是升级到 SQL Server 免费版还是您拥有该产品生产版本的 PID 密钥。 有关详细信息，请参阅 [SQL Server 2019 (的版本和支持的功能) ](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15) 和 [支持的版本和版本升级 (SQL Server 2016) ](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades?view=sql-server-ver15)。
1. 选择 " **下一步** "，直到到达 " **升级准备就绪** " 页，然后选择 " **升级**"。 在更改生效时，设置窗口可能会停止响应若干分钟。 **完整**的页面将确认升级已完成。 有关升级的分步过程，请参阅 [完整的步骤](https://docs.microsoft.com/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup?view=sql-server-ver15#procedure)。

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="完成页":::

如果 SQL Server 更改了版本，则除了更改版本以外，还需更新版本，并参考门户中的 " **验证版本和版本** " 部分来更改 SQL VM 实例。

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="更改版本元数据":::

## <a name="downgrade-the-version-of-sql-server"></a>降级 SQL Server 的版本

若要降级 SQL Server 版本，必须完全卸载 SQL Server，然后使用所需的版本重新安装它。 这类似于 SQL Server 的全新安装，因为你将无法从更高版本将早期版本的数据库还原到新安装的早期版本。 必须从头开始重新创建数据库。 如果在升级过程中还更改了版本的 SQL Server，则将 Azure 门户中 SQL Server VM 的 **版本** 属性更改为新版本值。 这会更新与此 VM 关联的元数据和计费。

> [!WARNING]
> 不支持 SQL Server 的就地降级。

可以按照以下步骤降级 SQL Server 的版本：

1. 请确保不使用 [更高版本中提供](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx)的任何功能。
1. 备份所有数据库，包括除 tempdb) 和用户数据库外的系统 (。
1. 将所有必需的服务器级对象 (如服务器触发器、角色、登录名、链接服务器、作业、凭据和证书) 。
1. 如果在早期版本中没有用于重新创建用户数据库的脚本，则必须使用 BCP.exe、SSIS 或 DACPAC 为所有对象编写脚本并导出所有数据。

   请确保在将此类项作为目标版本、依赖对象和高级选项编写脚本时选择正确的选项。

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="脚本编写选项":::

1. 完全卸载 SQL Server 和所有关联的服务。
1. 重启 VM。
1. 使用所需版本的程序的媒体安装 SQL Server。
1. 安装最新的 Service Pack 和累积更新。
1. 导入在步骤 3) 中导出的所有必需的服务器级对象 (。
1. 通过使用创建的脚本或步骤 4) 中的文件，从头开始重新创建所有必要的用户数据库 (。

## <a name="verify-the-version-and-edition-in-the-portal"></a>验证门户中的版本和版本

更改 SQL Server 版本后，请再次向 [SQL VM 资源提供程序](sql-vm-resource-provider-register.md) 注册 SQL Server VM，以便可以使用 Azure 门户查看 SQL Server 的版本。 列出的版本号现在应反映 SQL Server 安装的新升级版本。

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="验证版本":::

> [!NOTE]
> 如果已向 SQL VM 资源提供程序注册，请 [从 RP 注销](sql-vm-resource-provider-register.md#unregister-from-rp) ，然后再次 [注册 SQL VM 资源](sql-vm-resource-provider-register.md#register-with-rp) ，以便它检测到 VM 上安装的 SQL Server 的正确版本。 这会更新与此 VM 关联的元数据和计费信息。

## <a name="remarks"></a>注解

- 建议在升级完成后启动备份/更新统计信息/重建索引/检查一致性。 您还可以检查单独的数据库兼容性级别，以确保它们反映所需的级别。
- 在 VM 上更新 SQL Server 后，请确保 Azure 门户中 SQL Server 的 **版本** 属性与计费的已安装版本编号匹配。
- [更改版本](change-sql-server-edition.md#change-edition-in-portal)的功能是 SQL VM 资源提供程序的一项功能。 通过 Azure 门户部署 Azure 市场映像会自动将 SQL Server VM 注册到资源提供程序。 但是，自行安装 SQL Server 的客户必须手动 [注册其 SQL Server VM](sql-vm-resource-provider-register.md)。
- 如果删除 SQL Server VM 资源，则会还原映像的硬编码版本设置。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.md)
- [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
- [Windows VM 上的 SQL Server 发行说明](doc-changes-updates-release-notes.md)
