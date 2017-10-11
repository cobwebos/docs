让我们添加一个触发器。

1. 在逻辑应用设计器的搜索框中输入 *sftp*，并选择“SFTP - 添加或修改文件时”触发器。   
   ![SFTP 触发器图像 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. “添加或修改文件时”控件打开  
   ![SFTP 触发器图像 2](./media/connectors-create-api-sftp/trigger-2.png)  
3. 选择位于控件右侧的“...”。 这会打开文件夹选取器控件  
   ![SFTP 触发器图像 3](./media/connectors-create-api-sftp/action-1.png)  
4. 选择“SFTP”，以选择根文件夹作为要在其中监视新文件或已修改的文件的文件夹。 可以看到，根文件夹现已显示在“文件夹”控件中。  
   ![SFTP 触发器图像 4](./media/connectors-create-api-sftp/action-2.png)   

现在，逻辑应用上已配置了一个触发器。在特定的 SFTP 文件夹中修改或创建某个文件时，该触发器将开始运行工作流中的其他触发器和操作。 

> [!NOTE]
> 若要使逻辑应用正常工作，必须包含至少一个触发器和一个操作。 按照下一部分中的步骤添加操作。  
> 
> 

