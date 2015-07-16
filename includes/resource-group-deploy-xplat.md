## 如何使用 Azure CLI 进行部署

1. 登录到你的 Azure 帐户。

        azure login

  提供凭据后，该命令将返回你的登录结果。

        ...
        info:    login command OK

2. 如果你有多个订阅，请提供要用于部署的订阅 ID。

        azure account set <YourSubscriptionNameOrId>

3. 切换到“Azure 资源管理器”模块

        azure config mode arm

   你将收到新模式确认。

        info:     New mode is arm

4. 如果目前没有资源组，请创建新的资源组。提供资源组的名称，以及解决方案所需的位置。

        azure group create -n ExampleResourceGroup -l "West US"

   将返回新资源组的摘要。

        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            China North
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. 若要为资源组创建新部署，请运行以下命令并提供所需的参数。参数包括部署的名称、资源组的名称、所创建模板的路径，以及方案所需的任何其他参数。

   可以使用以下选项提供参数值：

   - 使用内联参数和本地模板。

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - 使用内联参数和模板链接。

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - 使用参数文件。

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

  部署资源组后，你将看到部署摘要。

         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK


6. 获取有关最新部署的信息。

         azure group log show -l ExampleResourceGroup

7. 获取有关部署错误的详细信息。

         azure group log show -l -v ExampleResourceGroup

<!---HONumber=HO63-->