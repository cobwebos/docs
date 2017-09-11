---
title: "创建私有 Docker 注册表 - Azure 门户 | Microsoft 文档"
description: "开始使用 Azure 门户创建和管理私有 Docker 容器注册表"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 7fbbb56d775ee96c9a44363a4e41d4fc3c630582
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-portal"></a>使用 Azure 门户创建私有 Docker 容器注册表
使用 Azure 门户创建容器注册表并管理其设置。 也可以使用 [Azure CLI 2.0 命令](container-registry-get-started-azure-cli.md)、[Azure PowerShell](container-registry-get-started-powershell.md) 或者以编程方式使用容器注册表 [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) 创建和管理容器注册表。

有关背景和概念，请参阅[概述](container-registry-intro.md)。

## <a name="create-a-container-registry"></a>创建容器注册表
1. 在 [Azure 门户](https://portal.azure.com)中，单击“+ 新建”。
2. 在应用商店中搜索“Azure 容器注册表”。
3. 选择发行商为“Microsoft”的“Azure 容器注册表”。
    ![Azure 应用商店中的容器注册表服务](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. 单击“创建” 。 此时会显示“Azure 容器注册表”边栏选项卡。

    ![容器注册表设置](./media/container-registry-get-started-portal/container-registry-settings.png)
5. 在“Azure 容器注册表”边栏选项卡中输入以下信息。 完成后，单击“创建”。

    a. **注册表名称**：特定注册表的全局唯一顶级域名。 在本示例中，注册表名称为 *myRegistry01*，但需要将它替换成自己的唯一名称。 该名称只能包含字母和数字。

    b. **资源组**：选择现有的[资源组](../azure-resource-manager/resource-group-overview.md#resource-groups)，或键入新资源组的名称。

    c. **位置**：选择[提供](https://azure.microsoft.com/regions/services/)该服务的 Azure 数据中心位置，例如“美国中南部”。

    d.单击“下一步”。 **管理员用户**：如果需要，请启用一个管理员用户来访问注册表。 创建注册表后可以更改此设置。

      > [!IMPORTANT]
      > 除了通过管理员用户帐户提供访问权限以外，容器注册表还基于 Azure Active Directory 服务主体的身份验证。 有关详细信息和注意事项，请参阅 [Authenticate with a container registry](container-registry-authentication.md)（使用容器注册表进行身份验证）。
      >

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 **存储帐户**：使用默认设置来创建[存储帐户](../storage/common/storage-introduction.md)，或选择位于同一位置的现有存储帐户。 当前不支持高级存储。

## <a name="manage-registry-settings"></a>管理注册表设置
创建注册表后，请在门户中的“容器注册表”在边栏选项卡上查找注册表设置。 例如，可能需要指定设置来登录注册表，或者需要启用或禁用管理员用户。

1. 在“容器注册表”边栏选项卡上，单击相应注册表的名称。

    ![容器注册表边栏选项卡](./media/container-registry-get-started-portal/container-registry-blade.png)
2. 若要管理访问权限设置，请单击“访问密钥”。

    ![容器注册表访问权限](./media/container-registry-get-started-portal/container-registry-access.png)
3. 请注意以下设置：

   * **登录服务器** - 用于登录注册表的完全限定名称。 在此示例中，它是 `myregistry01.azurecr.io`。
   * **管理员用户** - 启用或禁用注册表的管理员用户帐户。
   * **用户名**和**密码** - 可用于登录注册表的管理员用户帐户（如果已启用）的凭据。 可以根据需要重新生成密码。 将生成两个密码，这样就可以在重新生成某个密码的时候，使用另一个密码来连接到注册表。 若要改用服务主体进行身份验证，请参阅[使用私有 Docker 容器注册表进行身份验证](container-registry-authentication.md)。

## <a name="next-steps"></a>后续步骤
* [使用 Docker CLI 推送第一个映像](container-registry-get-started-docker-cli.md)

