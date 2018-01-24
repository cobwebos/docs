
特定函数应用中所有函数的代码位于根文件夹中，其中包含主机配置文件和一个或多个子文件夹。 每个子文件夹包含单独函数的代码，如以下示例所示：

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

host.json 文件包含一些特定于运行时的配置，位于函数应用的根文件夹中。 有关可用设置的信息，请参阅 [host.json 参考](../articles/azure-functions/functions-host-json.md)。

每个函数有一个对应的文件夹，其中包含一个或多个代码文件、function.json 配置和其他依赖项。

