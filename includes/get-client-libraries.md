### <a name="install-via-composer"></a>通过 Composer 安装
1. 在项目的根目录中创建名为 **composer.json** 的文件并向其添加以下代码：
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. 将 **[composer.phar][composer-phar]** 下载到项目根目录中。
3. 打开命令提示符并在项目根目录中执行以下命令
   
    ```
    php composer.phar install
    ```

或者转到 GitHub 上的 [Azure 存储 PHP 客户端库][php-sdk-github]，然后克隆源代码。

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
