---
title: 通过上传 CSV 文件批量删除组成员 - Azure 活动目录 |微软文档
description: 删除 Azure 管理中心中批量操作中的组成员。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e6d0752245e3864a8ad25efd5181d5cc1eec7ae
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533237"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>批量删除 Azure 活动目录中的组成员

使用 Azure 活动目录 （Azure AD） 门户，可以使用逗号分隔的值 （CSV） 文件批量删除组成员，从组中删除大量成员。

## <a name="to-bulk-remove-group-members"></a>批量删除组成员

1. 使用组织中的用户管理员帐户登录到[Azure 门户](https://portal.azure.com)。 组所有者还可以批量删除其拥有的组的成员。
1. 在 Azure AD 中，选择 **"对所有** > **组进行**分组"。
1. 打开要从中删除成员的组，然后选择 **"成员**"。
1. 在 **"成员"** 页上，选择 **"删除成员**"。
1. 在 **"批量删除组成员**"页上，选择 **"下载**"以获取具有所需组成员属性的 CSV 文件模板。

   !["删除成员"命令位于组的配置文件页上](./media/groups-bulk-remove-members/remove-panel.png)

1. 打开 CSV 文件，并为要从组中删除的每个组成员添加一行（所需值为成员对象 ID 或用户主体名称）。 然后保存文件。

   ![CSV 文件包含要删除的成员的名称和指示](./media/groups-bulk-remove-members/csv-file.png)

1. 在 **"批量删除组成员**"页上，**在"上传 csv 文件**"下，浏览到该文件。 选择该文件时，将开始验证 CSV 文件。
1. 验证文件内容后，批量导入页面将显示**已成功上载的文件**。 如果有错误，必须修正错误，然后才能提交作业。
1. 文件通过验证后，选择 **"提交"** 以启动 Azure 批量操作，从组中删除组成员。
1. 删除操作完成后，您将看到批量操作成功的通知。

## <a name="check-removal-status"></a>检查删除状态

您可以在 **"批量操作结果"** 页中查看所有挂起的批量请求的状态。

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

有关批量操作中每个行项的详细信息，请选择 **"成功****"、" 失败**"或 **"总请求"** 列下的值。 如果失败，则会列出失败原因。

## <a name="bulk-removal-service-limits"></a>批量拆卸服务限制

从中删除组成员列表的每个批量活动最多可以运行一个小时。 这允许删除至少 40，000 个成员的列表。

## <a name="next-steps"></a>后续步骤

- [批量导入组成员](groups-bulk-import-members.md)
- [下载组成员](groups-bulk-download-members.md)
- [下载所有组的列表](groups-bulk-download.md)
