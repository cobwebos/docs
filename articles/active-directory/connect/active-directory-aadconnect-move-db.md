---
title: 将 Azure AD Connect 数据库从 SQL Server Express 移到 SQL Server。 | Microsoft Docs
description: 本文档介绍如何将 Azure AD Connect 数据库从本地 SQL Server Express 服务器移到远程 SQL Server。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 03/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 16b8cb843b7081c2489f1b8048d896858c5424c2
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231750"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>将 Azure AD Connect 数据库从 SQL Server Express 移到 SQL Server 

本文档介绍如何将 Azure AD Connect 数据库从本地 SQL Server Express 服务器移到远程 SQL Server。  可使用以下过程完成此任务。

## <a name="about-this-scenario"></a>关于此方案
下面简单介绍此方案。  在此方案中，Azure AD Connect 版本 (1.1.819.0) 安装在单个 Windows Server 2016 域控制器上。  它使用内置的适用于其数据库的 SQL Server 2012 Express Edition。  此数据库将移到 SQL Server 2017 服务器。

![](media/active-directory-aadconnect-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>移动 Azure AD Connect 数据库
请执行以下步骤，将 Azure AD Connect 数据库移到远程 SQL Server。

1.  在 Azure AD Connect 服务器上转到“服务”，然后停止 **Microsoft Azure AD Sync** 服务。
2. 找到 **%Program Files%\Microsoft Azure AD Sync/Data/** 文件夹，将 **ADSync.mdf** 和 **ADSync_log.mdf** 文件复制到远程 SQL Server。
3. 在 Azure AD Connect 服务器上重启 **Microsoft Azure AD Sync** 服务。
4. 转到“控制面板”>“程序”>“程序和功能”，以便卸载 Azure AD Connect。  选择“Microsoft Azure AD Connect”，然后单击顶部的“卸载”。
5. 在远程 SQL Server 上，打开 SQL Server Management Studio。
6. 在“数据库”上右键单击，然后选择“附加”。
7. 在“附加数据库”屏幕上单击“添加”，导航到 ADSync.mdf 文件。  单击“确定”。
![](media/active-directory-aadconnect-move-db/move2.png)

8. 附加数据库以后，请返回到 Azure AD Connect 服务器并安装 Azure AD Connect。
9. MSI 安装完成后，将启动 Azure AD Connect 向导，进入快速模式安装。 单击“退出”图标关闭屏幕。
![欢迎使用](media/active-directory-aadconnect-existing-database/db1.png)
10. 启动新的命令提示符或 PowerShell 会话。 导航到 <drive>\program files\Microsoft Azure AD Connect 文件夹。 运行命令 .\AzureADConnect.exe /useexistingdatabase，在“使用现有数据库”安装模式下启动 Azure AD Connect 向导。
![PowerShell](media/active-directory-aadconnect-existing-database/db2.png)
11. 出现“欢迎使用 Azure AD Connect”屏幕。 同意许可条款和隐私声明后，单击“继续”。
![欢迎使用](media/active-directory-aadconnect-existing-database/db3.png)
12. 在“安装所需组件”屏幕上，“使用现有 SQL Server”选项已启用。 指定托管 ADSync 数据库的 SQL Server 的名称。 如果用于托管 ADSync 数据库的 SQL 引擎实例不是 SQL Server 上的默认实例，则必须指定 SQL 引擎实例名称。 此外，如果没有启用 SQL 浏览，还必须指定 SQL 引擎实例端口号。 例如：         
![欢迎使用](media/active-directory-aadconnect-existing-database/db4.png)           

13. 在“连接到 Azure AD”屏幕上，必须提供 Azure AD 目录的全局管理员凭据。 建议使用默认 onmicrosoft.com 域中的帐户。 此帐户仅用于在 Azure AD 中创建服务帐户，在向导完成后将不会使用。
![连接](media/active-directory-aadconnect-existing-database/db5.png)
 
14. 在“连接目录”屏幕上，为目录同步配置的现有 AD 林旁边显示有红色十字图标。 若要同步本地 AD 林中的更改，需要 AD DS 帐户。 Azure AD Connect 向导无法检索存储在 ADSync 数据库中的 AD DS 帐户凭据，因为凭据已加密，只能由先前的 Azure AD Connect 服务器进行解密。 单击“更改凭据”为 AD 林指定 AD DS 帐户。
![Directories](media/active-directory-aadconnect-existing-database/db6.png)
 
 
15. 在弹出对话框中，可以 (i) 提供企业管理员凭据，并让 Azure AD Connect 为你创建 AD DS 帐户，或 (ii) 自行创建 AD DS 帐户，并将其凭据提供给 Azure AD Connect。 选择一个选项并提供必要凭据后，单击“确定”关闭弹出对话框。
![欢迎使用](media/active-directory-aadconnect-existing-database/db7.png)
 
 
16. 提供凭据后，红色十字图标将被替换为绿色钩号图标。 单击“资源组名称” 的 Azure 数据工厂。
![欢迎使用](media/active-directory-aadconnect-existing-database/db8.png)
 
 
17. 在“准备好配置”屏幕上，单击“安装”。
![欢迎使用](media/active-directory-aadconnect-existing-database/db9.png)
 
 
18. 安装完成后，Azure AD Connect 服务器自动启用暂存模式。 建议在禁用暂存模式之前，查看服务器配置和意外更改的挂起导出。 

## <a name="next-steps"></a>后续步骤

- 了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。
- [使用现有 ADSync 数据库安装 Azure AD Connect](active-directory-aadconnect-existing-database.md)
- [使用 SQL 委派的管理员权限安装 Azure AD Connect](active-directory-aadconnect-sql-delegation.md)

