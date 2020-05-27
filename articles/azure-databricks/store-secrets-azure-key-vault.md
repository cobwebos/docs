---
title: 教程 - 通过 Azure Databricks 使用密钥保管库访问 blob 存储
description: 本教程介绍如何使用 Key Vault 中存储的机密从 Azure Databricks 访问 Azure Blob 存储。
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 026165c7c2052992e8ab485f9ab81c8964f38235
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647963"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>教程：使用 Azure Key Vault 从 Azure Databricks 访问 Azure Blob 存储

本教程介绍如何使用 Key Vault 中存储的机密从 Azure Databricks 访问 Azure Blob 存储。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建存储帐户和 Blob 容器
> * 创建 Azure Key Vault 并添加机密
> * 创建 Azure Databricks 工作区并添加机密范围
> * 从 Azure Databricks 访问 Blob 容器

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

> [!Note]
> 不能使用 Azure 免费试用订阅完成本教程  。
> 如果你有免费帐户，请转到个人资料并将订阅更改为“即用即付”  。 有关详细信息，请参阅 [Azure 免费帐户](https://azure.microsoft.com/free/)。 然后，[移除支出限制](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)，并为你所在区域的 vCPU [请求增加配额](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。 创建 Azure Databricks 工作区时，可以选择“试用版(高级 - 14天免费 DBU)”  定价层，让工作区访问免费的高级 Azure Databricks DBU 14 天。

## <a name="create-a-storage-account-and-blob-container"></a>创建存储帐户和 Blob 容器

1. 在 Azure 门户中，选择“创建资源” > “存储”。   然后选择“存储帐户”。 

   ![查找 Azure 存储帐户资源](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. 选择你的订阅和资源组，或创建新的资源组。 然后输入存储帐户名称，并选择一个位置。 选择“查看 + 创建”  。

   ![设置存储帐户属性](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. 如果验证失败，请解决问题并重试。 如果验证成功，请选择“创建”并等待创建存储帐户。 

4. 导航到新建的存储帐户，在“概述”页上的“服务”下选择“Blob”。    然后选择“+ 容器”并输入容器名称。  选择“确定”  。

   ![新建容器](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. 找到要上传到 Blob 存储容器的文件。 如果没有文件，请使用文本编辑器创建包含一些信息的新文本文件。 在本示例中，名为 **hw .txt** 的文件包含文本“hello world”。 在本地保存该文本文件，然后将其上传到 Blob 存储容器。

   ![将文件上传到容器](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. 返回你的存储帐户，在“设置”下选择“访问密钥”。   将“存储帐户名称”和“密钥 1”复制到文本编辑器，供稍后在本教程中使用。  

   ![查找存储帐户访问密钥](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>创建 Azure Key Vault 并添加机密

1. 在 Azure 门户中选择“创建资源”，然后在搜索框中输入 **Key Vault**。 

   ![创建 Azure 资源 - 搜索框](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. 系统会自动选择 Key Vault 资源。 选择“创建”  。

   ![创建 Key Vault 资源](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. 在“创建 Key Vault”页上输入以下信息，并将剩余字段保留默认值： 

   |properties|说明|
   |--------|-----------|
   |名称|Key Vault 的唯一名称。|
   |订阅|选择订阅。|
   |资源组|选择一个资源组，或创建新的资源组。|
   |位置|选择一个位置。|

   ![Azure Key Vault 属性](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. 提供上述信息后，选择“创建”  。 

4. 在 Azure 门户中导航到新建的 Key Vault，选择“机密”。  然后选择“+ 生成/导入”。  

   ![生成新的 Key Vault 机密](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. 在“创建机密”页上提供以下信息，并将剩余字段保留默认值： 

   |properties|值|
   |--------|-----------|
   |上传选项|手动|
   |名称|存储帐户密钥的易记名称。|
   |值|存储帐户中的密钥 1。|

   ![新 Key Vault 机密的属性](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. 在文本编辑器中保存密钥名称，供稍后在本教程中使用。然后选择“创建”。  导航到“属性”菜单。  将“DNS 名称”和“资源 ID”复制到文本编辑器，供稍后在本教程中使用。  

   ![复制 Azure Key Vault DNS 名称和资源 ID](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>创建 Azure Databricks 工作区并添加机密范围

1. 在 Azure 门户中，选择“创建资源”   >   “分析” >   “Azure Databricks”。

    ![Azure 门户上的 Databricks](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. 在“Azure Databricks 服务”下提供以下值，以创建 Databricks 工作区。 

   |properties  |说明  |
   |---------|---------|
   |工作区名称     | 提供 Databricks 工作区的名称        |
   |订阅     | 从下拉列表中选择自己的 Azure 订阅。        |
   |资源组     | 选择包含你的 Key Vault 的同一资源组。 |
   |位置     | 选择 Azure Key Vault 所在的同一位置。 有关所有可用区域，请参阅[各区域推出的 Azure 服务](https://azure.microsoft.com/regions/services/)。        |
   |定价层     |  选择“标准”或“高级”。   有关这些层的详细信息，请参阅 [Databricks 价格页](https://azure.microsoft.com/pricing/details/databricks/)。       |

   ![Databricks 工作区属性](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   选择“创建”  。

3. 在 Azure 门户中导航到新建的 Azure Databricks 资源，然后选择“启动工作区”。 

   ![启动 Azure Databricks 工作区](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. 在另一个窗口中打开 Azure Databricks 工作区后，将 **#secrets/createScope** 追加到 URL 中。 该 URL 应采用以下格式： 

   **https://<\location>.azuredatabricks.net/?o=<\orgID>#secrets/createScope**。
   

5. 输入范围名称，然后输入前面保存的 Azure Key Vault DNS 名称和资源 ID。 在文本编辑器中保存范围名称，供稍后在本教程中使用。 然后选择“创建”  。

   ![在 Azure Databricks 工作区中创建机密范围](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>从 Azure Databricks 访问 Blob 容器

1. 在 Azure Databricks 工作区的主页中，选择“常用任务”下的“新建群集”。  

   ![创建新的 Azure Databricks 笔记本](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. 输入群集名称，然后选择“创建群集”。  群集创建过程需要几分钟才能完成。

3. 创建群集后，导航到 Azure Databricks 工作区的主页，在“常用任务”下选择“新建笔记本”。  

   ![创建新的 Azure Databricks 笔记本](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. 输入笔记本名称，将语言设置为 Python。 将群集设置为在上一步骤中创建的群集的名称。

5. 运行以下命令以装载 Blob 存储容器。 请记得更改以下属性的值：

   * your-container-name
   * your-storage-account-name
   * mount-name
   * config-key
   * scope-name
   * key-name

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **mount-name** 是一个 DBFS 路径，表示 Blob 存储容器或该容器中的某个文件夹（在源中指定）要装载到的位置。
   * **conf-key** 可以是 `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` 或 `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **scope-name** 是在上一部分创建的机密范围的名称。 
   * **key-name** 是在 Key Vault 中为存储帐户密钥创建的机密的名称。

   ![在笔记本中创建 Blob 存储装入点](./media/store-secrets-azure-key-vault/command1.png)

6. 运行以下命令，将 Blob 存储容器中的文本文件读入数据帧。 请根据装入点名称和文件名更改命令中的值。

   ```python
   df = spark.read.text("/mnt/<mount-name>/<file-name>")
   ```

   ![将文件读入数据帧](./media/store-secrets-azure-key-vault/command2.png)

7. 使用以下命令显示文件的内容。

   ```python
   df.show()
   ```
   ![显示数据帧](./media/store-secrets-azure-key-vault/command3.png)

8. 若要卸载 Blob 存储，请运行以下命令：

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![卸载存储帐户](./media/store-secrets-azure-key-vault/command4.png)

9. 请注意，卸载装入点后，不再可以从 Blob 存储帐户读取数据。

   ![卸载存储帐户错误](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除整个资源组：

1. 在 Azure 门户的左侧菜单中，选择“资源组”并导航到你的资源组。 

2. 选择“删除资源组”并键入该资源组的名称。  然后选择“删除”  。 

## <a name="next-steps"></a>后续步骤

请继续学习下一篇教程，了解如何使用针对 Cosmos DB 启用的服务终结点实现 VNet 注入的 Databricks 环境。
> [!div class="nextstepaction"]
> [教程：使用 Cosmos DB 终结点实现 Azure Databricks](service-endpoint-cosmosdb.md)
