---
title: 将用户添加到 Azure Data Lake Analytics 帐户
description: 了解如何将用户正确添加到你的 Data Lake Analytics 帐户
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 58b78c7d9769069f36c9f01c2a7650878a6c5ec9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716783"
---
# <a name="adding-a-user-in-the-azure-portal"></a>在 Azure 门户中添加用户

## <a name="start-the-add-user-wizard"></a>启动添加用户向导
1. 通过 https://portal.azure.com 打开 Azure Data Lake Analytics。
2. 单击“添加用户向导”。
3. 在“选择用户”步骤中，找到想要添加的用户。 单击“选择”。
4. 在“选择角色”步骤中，选取“Data Lake Analytics 开发人员”。 此角色具有提交/监视/管理 U-SQL 作业所需的最小权限集。 如果组不旨在管理 Azure 服务，则分配给此角色。
5. 在“选择目录权限”步骤中，选择用户需要访问的任何其他数据库。 若要提交作业，需要拥有对 master 数据库的读写权限。 完成后，单击“确定”。
6. 在名为“分配所选权限”的最后一步，查看向导将进行的更改。 单击“确定”。


## <a name="configure-acls-for-data-folders"></a>为数据文件夹配置 ACL
根据需要，在包含输入数据和输出数据的文件夹上授予“R-X”或“RWX”。


## <a name="optionally-add-the-user-to-the-azure-data-lake-store-role-reader-role"></a>（可选）将用户添加到 Azure Data Lake Store 角色“读者”角色。
1.  找到你自己的 Azure Data Lake Store 帐户。
2.  单击“用户”。
3. 单击 **“添加”**。
4.  选择“Azure RBAC”角色以分配此组。
5.  分配给“读者”角色。 此角色具有浏览/管理存储在 ADLS 中的数据所需的最小权限集。 如果组不旨在管理 Azure 服务，则分配给此角色。
6.  键入组的名称。
7.  单击“确定”。

## <a name="adding-a-user-using-powershell"></a>使用 PowerShell 添加用户

1. 遵循本指南中的说明：[如何安装和配置 Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
2. 下载 [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell 脚本。
3. 运行 PowerShell 脚本。 

以下示例命令使用户能够提交作业、查看新的作业元数据和旧的元数据：

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>后续步骤

* [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [使用 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)

