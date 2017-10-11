#### <a name="prerequisites"></a>必备条件
* Azure 帐户;你可以创建[免费帐户](https://azure.microsoft.com/free)
* A [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3)帐户 

在逻辑应用中使用您的 OneDrive 帐户之前，授权逻辑应用，使连接到您的 OneDrive 帐户。  逻辑应用在 Azure 门户中可以轻松执行此操作。 

授权逻辑应用程序，以连接到你的 OneDrive 帐户使用以下步骤：

1. 创建一个逻辑应用。 在逻辑应用设计器中，选择**显示 Microsoft 托管 Api**在下拉列表，然后在搜索框中输入"onedrive"。 选择其中一个触发器或操作：  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. 如果你以前尚未创建任何连接到 OneDrive，会提示你使用你的 OneDrive 凭据进行登录：  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. 选择**登录**，然后输入你的用户名和密码。 选择**登录**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    这些凭据用于授权逻辑应用程序，以连接到，并访问您的 OneDrive 帐户中的数据。 
4. 选择**是**逻辑应用程序，以使用你的 OneDrive 帐户进行授权：  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. 请注意创建连接。 现在，继续执行应用程序逻辑中的其他步骤：  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

