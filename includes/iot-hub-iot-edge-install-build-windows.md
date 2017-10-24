## <a name="install-the-prerequisites"></a>安装必备组件

1. 安装 [Visual Studio 2015 或 2017](https://www.visualstudio.com)。 如果满足授权要求，可以使用免费的社区版。 请务必包含 Visual C++ 和 NuGet 包管理器。

1. 安装 [git](http://www.git-scm.com) 并确保可从命令行运行 git.exe。

1. 安装 [CMake](https://cmake.org/download/) 并确保可从命令行运行 cmake.exe。 建议使用 CMake 版本 3.7.2 或更高版本。 **.msi** 安装程序是 Windows 上最简单的选项。 安装程序提示时，至少为当前用户将 CMake 添加到 PATH。

1. 安装 [Python 2.7](https://www.python.org/downloads/release/python-27)。 请确保将 Python 添加到 `PATH` 环境变量。 转到“控制面板” > “系统和安全” > “系统” > “高级系统设置” > “环境变量”。 将 `C:\Python27` 添加到路径。 

1. 在命令提示符中，运行以下命令，将 Azure IoT Edge GitHub 存储库克隆到本地计算机上：

    ```cmd
    git clone https://github.com/Azure/iot-edge.git
    ```

## <a name="how-to-build-the-sample"></a>如何生成示例

现在可在本地计算机上生成 IoT Edge 运行时和示例：

1. 打开“VS 2015 开发人员命令提示”或“VS 2017 开发人员命令提示”，具体要取决于你的版本。

1. 浏览到 **iot-edge** 存储库本地副本中的根文件夹。

1. 如下所示运行生成脚本：

    ```cmd
    tools\build.cmd --disable-native-remote-modules
    ```

此脚本创建 Visual Studio 解决方案文件并生成解决方案。 可以在 **iot-edge** 存储库本地副本的 **build** 文件夹中找到 Visual Studio 解决方案。 如果想要生成并运行单元测试，请添加 `--run-unittests` 参数。 如果想要生成并运行端到端测试，请添加 `--run-e2e-tests`。

> [!NOTE]
> 每次运行 build.cmd 脚本时，都会删除 iot-edge 存储库本地副本的根文件夹中的 build 文件夹并重新生成。