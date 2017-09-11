### <a name="prerequisites"></a>先决条件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* [Azure Blob 存储帐户](../articles/storage/common/storage-create-storage-account.md)，其中包括存储帐户名称及其访问密钥。 在 Azure 门户中，该信息列于存储帐户的属性中。 详细了解 [Azure 存储](../articles/storage/common/storage-introduction.md)。

在逻辑应用中使用 Azure Blob 存储帐户之前，请先连接到 Azure Blob 存储帐户。 可以在 Azure 门户中的逻辑应用内轻松地执行此操作。  

按照以下步骤操作，即可连接到 Azure Blob 存储帐户：  

1. 创建逻辑应用。 在逻辑应用设计器中，添加触发器，并添加操作。 在下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“blob”。 选择其中一个操作：  
   
    ![Azure Blob 存储连接创建步骤](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. 如果以前尚未创建任何与 Azure 存储的连接，系统会提示输入连接详细信息：   
   
    ![Azure Blob 存储连接创建步骤](./media/connectors-create-api-azureblobstorage/connection-details.png)  
3. 输入存储帐户详细信息。 带有星号的属性必填。
   
   | 属性 | 详细信息 |
   | --- | --- |
   | 连接名称 * |为连接输入任何名称。 |
   | Azure 存储帐户名称 * |输入存储帐户名称。 在 Azure 门户中，存储帐户名称显示在存储属性中。 |
   | Azure 存储帐户访问密钥 * |输入存储帐户密钥。 在 Azure 门户中，访问密钥显示在存储属性中。 |
   
    这些凭据用于授权逻辑应用进行连接，然后访问数据。 
4. 选择“创建” 。
5. 请注意，连接已创建。 现在，继续在逻辑应用中执行其他步骤： 
   
    ![Azure Blob 存储连接创建步骤](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

