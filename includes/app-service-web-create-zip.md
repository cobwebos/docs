## <a name="create-a-project-zip-file"></a>创建一个项目 ZIP 文件

创建一个包含项目中的所有内容的 ZIP 存档。 以下命令使用终端中的默认工具：

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

随后将此 ZIP 文件上传到 Azure 并将其部署到应用服务。