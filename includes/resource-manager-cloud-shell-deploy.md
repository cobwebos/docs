## <a name="deploy-template-from-cloud-shell"></a>从 Cloud Shell 部署模板

可以使用 [Cloud Shell](../articles/cloud-shell/overview.md) 来部署模板。 但是，必须先将模板加载到 Cloud Shell 的存储帐户。 如果尚未使用过 Cloud Shell，请参阅 [Azure Cloud Shell 概述](../articles/cloud-shell/overview.md)，了解如何设置它。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择 Cloud Shell 资源组。 名称模式为 `cloud-shell-storage-<region>`。

   ![选择资源组](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. 选择适用于 Cloud Shell 的存储帐户。

   ![选择存储帐户](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. 选择“Blob”。

   ![选择 Blob](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. 选择“+ 容器”。

   ![添加容器](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. 为容器指定名称和访问级别。 本文中的示例模板未包含任何敏感信息，因此允许进行匿名读取访问。 选择“确定”。

   ![提供容器值](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. 选择你创建的容器。

   ![选择新容器](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. 选择“上传”。

   ![上传 blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. 找到并上传模板。

   ![上传文件](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. 在它上传后，选择模板。

   ![选择新模板](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. 复制 URL。

   ![复制 URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. 打开提示符。

   ![打开 Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
