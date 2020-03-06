---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: 3cd9d017429b629acad39f5b902e842886c3c818
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304966"
---
## <a name="configure-the-data-source"></a>配置数据源

执行以下步骤，将 Azure 数据资源管理器配置为你的仪表板工具的数据源。 我们将在本节中更详细地介绍这些步骤：

1. 创建 Azure Active Directory (Azure AD) 服务主体。 您的仪表板工具使用该服务主体来访问 Azure 数据资源管理器服务。

1. 将 Azure AD 服务主体添加到 Azure 数据资源管理器数据库中的“查看者”角色。

1. 根据 Azure AD 服务主体中的信息指定仪表板工具连接属性，并测试连接。

### <a name="create-a-service-principal"></a>创建服务主体

可在 [Azure 门户](#azure-portal)中或使用 [Azure CLI](#azure-cli) 命令行创建服务主体。 无论使用哪种方法，创建后都会获得四个连接属性的值，将在后面的步骤中用到这些值。

#### <a name="azure-portal"></a>Azure 门户

1. 要创建服务主体，请按照 [Azure 门户文档](/azure/active-directory/develop/howto-create-service-principal-portal)中的说明进行操作。

    1. 在[将应用程序分配给角色](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)部分，将“读取者”的角色类型分配给 Azure 数据资源管理器群集。

    1. 在 "[获取登录的值](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)" 部分中，复制 "步骤：**目录 ID** （租户 id）"、"**应用程序 id**" 和 "**密码**" 中介绍的三个属性值。

1. 在 Azure 门户中，选择“订阅”，然后复制在其中创建服务主体的订阅的 ID。

    ![订阅 ID - 门户](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. 创建服务主体。 设置适用范围和角色类型 `reader`。

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    有关详细信息，请参阅[使用 Azure CLI 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)。

1. 该命令返回如下结果集。 复制三个属性值：appID、密码和租户。


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. 获取订阅的列表。

    ```azurecli
    az account list --output table
    ```

    复制相应的订阅 ID。

    ![订阅 ID - CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>将服务主体添加到查看者角色

现在已有服务主体，可将其添加到 Azure 数据资源管理器数据库中的“查看者”角色。 可在 Azure 门户中的“权限”下执行此任务，也可以使用管理命令在“查询”下执行此任务。

#### <a name="azure-portal---permissions"></a>Azure 门户 - 权限

1. 在 Azure 门户中，转到 Azure 数据资源管理器群集。

1. 在“概述”部分中，选择包含 StormEvents 样本数据的数据库。

    ![选择数据库](media/data-explorer-configure-data-source/select-database.png)

1. 选择“权限”，然后选择“添加”。

    ![数据库权限](media/data-explorer-configure-data-source/database-permissions.png)

1. 在“添加数据库权限”下，选择“查看者”角色，然后选择“选择主体”。

    ![添加数据库权限](media/data-explorer-configure-data-source/add-permission.png)

1. 搜索你创建的服务主体。 选择主体，然后单击“选择”。

    ![在 Azure 门户中管理权限](media/data-explorer-configure-data-source/new-principals.png)

1. 选择“保存”。

    ![在 Azure 门户中管理权限](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>管理命令 - 查询

1. 在 Azure 门户中，转到 Azure 数据资源管理器群集，然后选择“查询”。

    ![查询](media/data-explorer-configure-data-source/query.png)

1. 在查询窗口中运行以下命令。 使用 Azure 门户或 CLI 中的应用程序 ID 和租户 ID。

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    该命令返回如下结果集。 在此示例中，第一行用于数据库中的现有用户，第二行用于刚刚添加的服务主体。

    ![结果集](media/data-explorer-configure-data-source/result-set.png)
