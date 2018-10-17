## <a name="create-a-project-zip-file"></a>创建一个项目 zip 文件

请确保仍处于示例项目的根目录中。 创建一个包含项目中的所有内容的 zip 存档。 以下命令使用终端中的默认工具：

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

随后将此 ZIP 文件上传到 Azure 并将其部署到应用服务。