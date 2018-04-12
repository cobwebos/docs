### <a name="prerequisites"></a>先决条件
* 一个 [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol) 帐户  

可以在逻辑应用中使用 FTP 帐户之前，必须先授权该逻辑应用可以连接到 FTP 帐户。好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。  

下面介绍授权逻辑应用连接到 FTP 帐户的步骤：  

1. 要创建与 FTP 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“FTP”。 选择要使用的触发器或操作：  
   ![FTP 连接创建步骤](./media/connectors-create-api-ftp/ftp-1.png)  
2. 如果以前尚未创建任何与 FTP 的连接，系统会提示提供 FTP 凭据。 这些凭据将用于授权逻辑应用连接到 FTP 帐户，并访问该帐户中的数据：  
   ![FTP 连接创建步骤](./media/connectors-create-api-ftp/ftp-2.png)  
3. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤：  
   ![FTP 连接创建步骤](./media/connectors-create-api-ftp/ftp-3.png)  

