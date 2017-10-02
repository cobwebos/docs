在 Azure 上运行时，有些软件包可能无法使用 pip 进行安装。  可能只是该软件包在 Python 软件包索引中不可用。  可能需要一个编译器（在运行 Azure 应用服务中的 Web 应用的计算机上未提供编译器）。

在此部分中，我们将考察解决此问题的方法。

### <a name="request-wheels"></a>请求轮
如果软件包安装需要编译器，应尝试联系软件包所有者以请求为软件包提供轮。

通过最新提供的[用于 Python 2.7 的 Microsoft Visual C++ 编译器][Microsoft Visual C++ Compiler for Python 2.7]，现在可以更轻松地构建具有针对 Python 2.7 的本机代码的软件包。

### <a name="build-wheels-requires-windows"></a>构建轮（需要 Windows）
注意：使用此选项时，确保使用匹配 Azure 应用服务中 Web 应用上所用的平台/体系结构/版本（Windows/32 位/2.7 或 3.4）的 Python 环境编译此软件包。

如果软件包由于需要编译器而未安装，可以在本地计算机上安装编译器，然后为软件包构建轮，随后将轮包含在存储库中。

Mac/Linux 用户：如果没有 Windows 计算机的访问权限，请参阅[创建运行 Windows 的虚拟机][Create a Virtual Machine Running Windows]，了解如何在 Azure 上创建 VM。  可以使用它来构建轮、将其添加到存储库以及在必要时放弃虚拟机。 

对于 Python 2.7，可安装[用于 Python 2.7 的 Microsoft Visual C++ 编译器][Microsoft Visual C++ Compiler for Python 2.7]。

对于 Python 3.4，可安装 [Microsoft Visual C++ 2010 Express][Microsoft Visual C++ 2010 Express]。

要构建轮，需要轮软件包：

    env\scripts\pip install wheel

将使用 `pip wheel` 编译依赖项：

    env\scripts\pip wheel azure==0.8.4

这会在 \wheelhouse 文件夹中创建 .whl 文件。  将 \wheelhouse 文件夹和轮文件添加到存储库。

编辑 requirements.txt，在顶部添加 `--find-links` 选项。 这会让 pip 在本地文件夹中查找完全匹配项，并转至 python 软件包索引。

    --find-links wheelhouse
    azure==0.8.4

如果想要将所有依赖项包含在 \wheelhouse 文件夹中而根本不使用 python 软件包索引，则可以通过将 `--no-index` 添加到 requirements.txt 之上来强制 pip 忽略软件包索引。

    --no-index

### <a name="customize-installation"></a>自定义安装
可以自定义部署脚本以在虚拟环境中使用备用安装程序（例如 easy\_install）来安装软件包。  请参阅 deploy.cmd 以了解注释掉的示例。确保此类软件包不列入 requirements.txt 中，从而防止 pip 将其安装。

将其添加到部署脚本：

    env\scripts\easy_install somepackage

还可以使用 easy\_install 从 exe 安装程序进行安装（有些兼容 zip，所以 easy\_install 支持它们）。  将安装程序添加到存储库，然后通过传递可执行文件的路径来调用 easy\_install。

将其添加到部署脚本：

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>将虚拟环境包含在存储库中（需要 Windows）
注意：使用此选项时，确保使用匹配 Azure 应用服务中 Web 应用上所用的平台/体系结构/版本（Windows/32 位/2.7 或 3.4）的虚拟环境。

如果存储库中包含虚拟环境，可以通过创建一个空文件来防止部署脚本在 Azure 上执行虚拟环境管理：

    .skipPythonDeployment

建议删除应用上的现有虚拟环境，防止在自动管理虚拟环境时出现剩余文件。

[Create a Virtual Machine Running Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ Compiler for Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
