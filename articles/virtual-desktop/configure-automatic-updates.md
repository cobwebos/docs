---
title: 配置 Microsoft Endpoint Configuration Manager-Azure
description: 如何配置 Microsoft Endpoint Configuration Manager 将软件更新部署到 Windows 10 企业版上的 windows 虚拟机。
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: lizross
ms.openlocfilehash: bfc797e0c11a1804d27c6c97fea8cb2ba62dca7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010117"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>配置 Microsoft 端点 Configuration Manager

本文介绍如何配置 Microsoft Endpoint Configuration Manager 以自动将更新应用到运行 Windows 10 企业多会话的 Windows 虚拟桌面主机。

## <a name="prerequisites"></a>必备条件

若要配置此设置，需执行以下操作：

   - 请确保已在虚拟机上安装了 Microsoft 端点 Configuration Manager 代理。
   - 请确保 Microsoft Endpoint Configuration Manager 的版本至少在分支级别1906上。 为了获得最佳结果，请使用分支级别1910或更高版本。

## <a name="configure-the-software-update-point"></a>配置软件更新点

若要接收 Windows 10 企业多会话的更新，你需要启用 Windows Server 版本1903及更高版本，作为 Microsoft 端点 Configuration Manager 中的产品。 如果使用 Windows Server Update 服务将更新部署到系统，则此产品设置也适用。

接收更新：

1. 打开 Microsoft 终结点 Configuration Manager，然后选择 " **站点**"。
2. 选择 " **配置站点组件**"。
3. 在下拉菜单中选择 " **软件更新点** "。
4. 选择**产品**选项卡。
5. 选中 " **Windows Server 版本1903及更高版本**" 复选框。
6. 请参阅**软件库**  >  **概述**  >  **软件更新**  >  **所有软件更新**，并选择**同步软件更新**。
7. 检查**Program Files**  >  **Microsoft Configuration Manager**日志中的 wsyncmgr.log 文件  >  **Logs** ，确保更改已保存。 同步更新可能需要几分钟时间。

## <a name="create-a-query-based-collection"></a>创建基于查询的集合

若要创建 Windows 10 企业多会话虚拟机的集合，可以使用基于查询的集合来标识特定的操作系统 SKU。

创建集合：

1. 选择 " **资产和符合性**"。
2. 中转到 "**概述**"  >  "**设备**集合"，右键单击 "**设备集合**"，然后从下拉菜单中选择 "**创建设备集合**"。
3. 在打开的菜单的 " **常规** " 选项卡中，在 " **名称** " 字段中输入描述您的集合的名称。 在 " **注释** " 字段中，可以指定描述集合内容的其他信息。 在 " **限制集合**" 中，定义集合查询中包含的计算机。
4. 在 " **成员身份规则** " 选项卡中，选择 " **添加规则**"，然后选择 " **查询规则**"，为查询添加规则。
5. 在 " **查询规则属性**" 中，输入规则的名称，然后通过选择 " **编辑查询语句**" 定义规则的参数。
6. 选择 " **显示查询语句**"。
7. 在语句中，输入以下字符串：

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. 选择 **"确定"** 以创建集合。
9. 若要检查是否已成功创建集合，请参阅 "**资产和符合性**  >  **概述**" "  >  **设备集合**"。
