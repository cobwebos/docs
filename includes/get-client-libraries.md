### <a name="install-via-composer"></a>通过 Composer 安装
1. [安装 Git][install-git]。 请注意，在 Windows 上，还需要向 PATH 环境变量添加 Git 可执行文件。 
2. 在项目的根目录中创建名为 **composer.json** 的文件并向其添加以下代码：
   
    ```
    {
      "require": {
        "microsoft/windowsazure": "^0.4"
      }
    }
    ```
3. 将 **[composer.phar][composer-phar]** 下载到项目根目录中。
4. 打开命令提示符并在项目根目录中执行以下命令
   
    ```
    php composer.phar install
    ```

[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
