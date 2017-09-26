## <a name="transfer-local-files-to-cloud-shell"></a>将本地文件传输到 Cloud Shell
`clouddrive` 目录与 Azure 门户存储边栏选项卡同步。 使用此边栏选项卡将本地文件传输到文件共享或从文件共享传输到本地文件。 刷新边栏选项卡时，从 Cloud Shell 中更新文件将反映在文件存储 GUI 中。

### <a name="download-files"></a>下载文件

![本地文件列表](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. 在 Azure 门户中，转到已装载的文件共享。
2. 选择目标文件。
3. 选择“下载”按钮。

### <a name="upload-files"></a>上传文件

![要上传的本地文件](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. 请转到已装在的文件共享。
2. 选择“上传”按钮。
3. 选择要上传的文件。
4. 确认上传。

现在应可在 Cloud Shell 的 `clouddrive` 目录中查看可访问的文件。