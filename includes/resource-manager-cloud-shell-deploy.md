## <a name="deploy-template-from-cloud-shell"></a>从 Cloud Shell 部署模板

可以使用 [Cloud Shell](../articles/cloud-shell/overview.md) 来部署模板。 但是，必须先将模板加载到 Cloud Shell 的文件共享。 如果尚未使用过 Cloud Shell，请参阅 [Azure Cloud Shell 概述](../articles/cloud-shell/overview.md)，了解如何设置它。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择 Cloud Shell 资源组。 名称模式为 `cloud-shell-storage-<region>`。

   ![选择资源组](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. 选择适用于 Cloud Shell 的存储帐户。

   ![选择存储帐户](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. 选择“文件”。

   ![选择文件](./media/resource-manager-cloud-shell-deploy/select-files.png)

1. 选择 Cloud Shell 的文件共享。 名称模式为 `cs-<user>-<domain>-com-<uniqueGuid>`。

   ![选择文件共享](./media/resource-manager-cloud-shell-deploy/select-file-share.png)

1. 选择“添加目录”。

   ![添加目录](./media/resource-manager-cloud-shell-deploy/select-add-directory.png)

1. 将其命名为“模板”，然后选择“确定”。

   ![为目录命名](./media/resource-manager-cloud-shell-deploy/name-templates.png)

1. 选择新目录。

   ![选择目录](./media/resource-manager-cloud-shell-deploy/select-templates.png)

1. 选择“上传”。

   ![选择“上传”](./media/resource-manager-cloud-shell-deploy/select-upload.png)

1. 找到并上传模板。

   ![上传文件](./media/resource-manager-cloud-shell-deploy/upload-files.png)

1. 打开提示符。

   ![打开 Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
