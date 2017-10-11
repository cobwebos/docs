Azure 将确定你的应用程序使用 Python**如果这些条件都为 true**:

* 根文件夹中的 requirements.txt 文件
* 根文件夹中的任何.py 文件或指定 python 的 runtime.txt

这种情况时，它将使用的 Python 特定部署脚本，后者将执行的文件，以及其他 Python 操作，如的标准同步：

* 自动管理虚拟环境
* 使用 pip 的 requirements.txt 中列出的包安装
* 基于所选的 Python 版本的相应 web.config 的创建。
* 收集 Django 应用程序的静态文件

你可以控制默认部署步骤的某些方面，而无需自定义脚本。

如果你想要跳过所有 Python 具体的部署步骤，你可以创建此空文件：

    \.skipPythonDeployment

如果你想要跳过为 Django 应用程序的静态文件的集合：

    \.skipDjango 

更灵活地控制部署，你可以通过创建以下文件重写默认部署脚本：

    \.deployment
    \deploy.cmd

你可以使用[Azure 命令行界面][ Azure command-line interface]以创建该文件。  从你的项目文件夹使用以下命令：

    azure site deploymentscript --python

当这些文件不存在时，Azure 将创建临时部署脚本，并运行它。  它等同于使用上面的命令创建一个。

[Azure command-line interface]: http://azure.microsoft.com/downloads/
