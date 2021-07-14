## 包管理工具scoop

可以管理程序软件包的安装工具，可以管理操作系统上的软件，我们熟知的 MacOs 上的`homebrew`都是软件管理工具











> 改变scoop默认安装地址

默认安装的软件都在 `C:\users\scoop`目录下，我们可以移除C盘

```shell
$env:SCOOP='D:\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')
```

注意这个时候要将scoop的原本的程序从C:\Users{你的用户名}\scoop目录移到你自定义的目录下：