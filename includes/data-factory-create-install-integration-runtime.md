## <a name="create-a-self-hosted-integration-runtime"></a>创建自承载 Integration Runtime

在本部分，请创建一个自承载 Integration Runtime，然后将其与安装了 SQL Server 数据库的本地计算机相关联。 自承载 Integration Runtime 是一个组件，用于将数据从计算机上的 SQL Server 复制到 Azure Blob 存储。 

1. 创建一个适用于 Integration Runtime 名称的变量。 使用唯一的名称，并记下它。 本教程后面部分需要使用它。 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. 创建自我托管的集成运行时。 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   下面是示例输出：

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 
3. 若要检索所创建的 Integration Runtime 的状态，请运行以下命令。 确认 **State** 属性的值已设置为 **NeedRegistration**。 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   下面是示例输出：

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

4. 若要检索用于将自承载 Integration Runtime 注册到云中 Azure 数据工厂服务的身份验证密钥，请运行以下命令： 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   下面是示例输出：

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. 复制其中一个密钥（去除双引号），用于注册将在以下步骤中安装到计算机上的自承载 Integration Runtime。  

## <a name="install-the-integration-runtime"></a>安装 Integration Runtime
1. 如果已在计算机上安装 Integration Runtime，请使用“添加或删除程序”将其卸载。 

2. 将自承载 Integration Runtime [下载](https://www.microsoft.com/download/details.aspx?id=39717)到本地 Windows 计算机上。 进行安装。

3. 在“欢迎使用 Microsoft Integration Runtime 安装程序”页上，选择“下一步”。

4. 在“最终用户许可协议”页上接受许可协议的条款，然后选择“下一步”。

5. 在“目标文件夹”页上选择“下一步”。

6. 在“准备安装 Microsoft Integration Runtime”页上选择“安装”。

7. 如果看到一条警告消息，指出系统正将计算机配置为在不使用时进入睡眠或休眠模式，请选择“确定”。

8. 如果看到“电源选项”页，请将其关闭，然后转到安装页。

9. 在“完成 Microsoft Integration Runtime 安装程序”页上选择“完成”。

10. 在“注册 Integration Runtime (自承载)”页上粘贴在上一部分保存的密钥，然后选择“注册”。 

    ![注册 Integration Runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

11. 成功注册自承载 Integration Runtime 后，会看到以下消息：

    ![已成功注册](media/data-factory-create-install-integration-runtime/registered-successfully.png)

12. 在“新建 Integration Runtime (自承载)节点”页上，选择“下一步”。 

    ![“新建 Integration Runtime 节点”页](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)

13. 在“Intranet 信道”页上，选择“跳过”。 选择 TLS/SSL 认证，确保多节点 Integration Runtime 环境中的节点内通信安全。 

    ![“Intranet 信道”页](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)

14. 在“注册 Integration Runtime (自承载)”页上，选择“启动配置管理器”。

15. 将节点连接到云服务后，会看到以下页：

    ![“节点已连接”页](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. 现在，请测试到 SQL Server 数据库的连接。

    ![“诊断”选项卡](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. 在“配置管理器”页上，转到“诊断”选项卡。

    b. 选择“SqlServer”作为数据源类型。

    c. 输入服务器名称。

    d. 输入数据库名称。

    e. 选择身份验证模式。

    f. 输入用户名。

    g. 输入用户名的密码。

    h. 若要确认 Integration Runtime 能否连接到 SQL Server，请选择“测试”。 如果连接成功，则会看到绿色复选标记。 如果连接不成功，则会看到错误消息。 请解决问题，确保 Integration Runtime 可以连接到 SQL Server。    

    > [!NOTE]
    > 记下身份验证类型、服务器、数据库、用户和密码的值。 本教程后面会用到它们。 
    
