---
title: 为容器 (预览) 启用和管理软删除
titleSuffix: Azure Storage
description: 启用容器软删除 (预览) 以便在错误地修改或删除数据时更轻松地恢复数据。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4b25458cc934097b5477c174ae7ecc47762e2929
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91280294"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>为容器 (预览) 启用和管理软删除

容器软删除 (预览) 可防止意外或错误地修改或删除数据。 为存储帐户启用容器软删除后，在指定的保持期内，容器及其内容可能会被删除。

如果某个应用程序或其他存储帐户用户可能无意中修改或删除了数据，则 Microsoft 建议启用容器软删除。 本文介绍如何为容器启用软删除。 有关容器软删除的详细信息（包括如何注册预览版），请参阅 [容器的软删除 (预览) ](soft-delete-container-overview.md)。

对于端到端的数据保护，Microsoft 建议同时为 blob 和 Blob 版本控制启用软删除。 若要了解如何为 blob 启用软删除，请参阅为 [Blob 启用和管理软删除](soft-delete-blob-enable.md)。 若要了解如何启用 blob 版本控制，请参阅 [blob 版本控制](versioning-overview.md)。

## <a name="enable-container-soft-delete"></a>启用容器软删除

可以通过使用 Azure 门户或 Azure 资源管理器模板，随时为存储帐户启用或禁用容器软删除。

# <a name="portal"></a>[门户](#tab/azure-portal)

若要使用 Azure 门户为存储帐户启用容器软删除，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中导航到存储帐户。
1. 在 " **Blob 服务**" 下找到**数据保护**设置。
1. 将 " **容器软删除** " 属性设置为 " *已启用*"。
1. 在 " **保留策略**" 下，指定软删除容器由 Azure 存储保留多长时间。
1. 保存所做更改。

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="显示如何在 Azure 门户中启用容器软删除的屏幕截图":::

# <a name="template"></a>[模板](#tab/template)

若要使用 Azure 资源管理器模板启用容器软删除，请创建用于设置 **containerDeleteRetentionPolicy** 属性的模板。 以下步骤介绍如何在 Azure 门户中创建模板。

1. 在 Azure 门户中，选择 " **创建资源**"。
1. 在 **"搜索应用商店"** 中，键入 **模板部署**，然后按 **enter**。
1. 选择 " **模板部署**"，选择 " **创建**"，然后选择 **"在编辑器中生成自己的模板"**。
1. 在模板编辑器中，粘贴以下 JSON。 将  `<account-name>`   占位符替换为你的存储帐户的名称。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. 指定保持期。 默认值为 7。
1. 保存模板。
1. 指定帐户的资源组，然后选择 "查看" 和 " **创建**"   按钮以部署模板并启用容器软删除。

## <a name="view-soft-deleted-containers"></a>查看软删除的容器

启用软删除后，可以在 Azure 门户中查看软删除容器。 软删除容器在指定的保留期内可见。 保持期到期后，软删除容器将被永久删除，并且不再可见。

若要查看 Azure 门户中软删除的容器，请执行以下步骤：

1. 在 Azure 门户中导航到存储帐户，并查看容器的列表。
1. 切换 "显示删除的容器" 切换到列表中包含已删除的容器。

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="显示如何在 Azure 门户中启用容器软删除的屏幕截图":::

## <a name="restore-a-soft-deleted-container"></a>还原软删除容器

可以在保留期内还原软删除容器及其内容。 若要还原 Azure 门户中软删除的容器，请执行以下步骤：

1. 在 Azure 门户中导航到存储帐户，并查看容器的列表。
1. 显示要还原的容器的上下文菜单，然后从菜单中选择 " **撤消删除** "。

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="显示如何在 Azure 门户中启用容器软删除的屏幕截图":::

## <a name="next-steps"></a>后续步骤

- [容器的软删除 (预览) ](soft-delete-container-overview.md)
- [blob 的软删除](soft-delete-blob-overview.md)
- [Blob 版本控制](versioning-overview.md)
