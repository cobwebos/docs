---
title: 将应用移动到其他区域
description: 了解如何将应用服务资源从一个区域移动到另一个区域。
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 87a2da31802a8b6858e875c23ef1dbd2d6d006bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86524848"
---
# <a name="move-an-app-service-resource-to-another-region"></a>将应用服务资源移动到其他区域

本文介绍了如何将应用服务资源移动到其他 Azure 区域。 由于各种原因，你可能需要将资源移动到其他区域。 例如，利用新的 Azure 区域，部署仅在特定区域中可用的功能或服务，满足内部策略和监管要求，或者满足容量规划要求。

应用服务资源是特定于区域的，不能跨区域移动。 必须在目标区域中创建现有应用服务资源的副本，然后将内容移动到新应用。 如果你的源应用使用自定义域，你可以在完成后[将其迁移到目标区域中的新应用](manage-custom-dns-migrate-domain.md)。

为了更轻松地复制应用，可以[将个体应用服务应用克隆](app-service-web-app-cloning.md)到另一个区域中的应用服务计划，但它具有[限制](app-service-web-app-cloning.md#current-restrictions)，特别是它不支持 Linux 应用。

## <a name="prerequisites"></a>先决条件

- 确保应用服务应用位于要从中进行移动的 Azure 区域中。
- 确保目标区域支持你要移动其资源的应用服务和任何相关服务。
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>准备

查明你当前正在使用的所有应用服务资源。 例如：

- 应用服务应用
- [应用服务计划](overview-hosting-plans.md)
- [部署槽位](deploy-staging-slots.md)
- [在 Azure 中购买的自定义域](manage-custom-dns-buy-domain.md)
- [SSL 证书](configure-ssl-certificate.md)
- [Azure 虚拟网络集成](web-sites-integrate-with-vnet.md)
- [混合连接](app-service-hybrid-connections.md)。
- [托管标识](overview-managed-identity.md)
- [备份设置](manage-backup.md)

某些资源（例如导入的证书或混合连接）包含与其他 Azure 服务的集成。 有关如何跨区域移动这些资源的信息，请参阅相应服务的文档。

## <a name="move"></a>移动

1. [创建源应用的备份](manage-backup.md)。
1. [在目标区域中的新应用服务计划中创建一个应用](app-service-plan-manage.md#create-an-app-service-plan)。
2. [在目标应用中还原备份](web-sites-restore.md)
2. 如果你使用自定义域，请使用 `awverify.`[提前将其绑定到目标应用](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively)，并[在目标应用中启用该域](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app)。
3. 将目标应用中的其他所有内容配置为与源应用相同，并验证你的配置。
4. 当你已准备好将自定义域指向目标应用时，[重新映射域名](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)。

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>清理源资源

删除源应用和应用服务计划。 [非免费层级中的应用服务计划会产生费用，即使其中没有正在运行的应用也是如此。](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>后续步骤

[使用 PowerShell 克隆 Azure 应用服务应用](app-service-web-app-cloning.md)