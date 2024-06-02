# 基于Hugo搭建个人博客


# <center>🚀 Hugo Quick Start 🚀</center>

> 本文参考：
> [Hugo快速开始中文文档](https://www.gohugo.org/doc/overview/quickstart/)

## 第一步：安装Hugo
1. 下载所需操作系统对应的版本，本文以windows为例，因此下载
[hugo_extended_0.126.2_windows-amd64.zip](https://github.com/gohugoio/hugo/releases/download/v0.126.2/hugo_extended_0.126.2_windows-amd64.zip)。
    > 其他操作系统下载链接：[Hugo releases](https://github.com/gohugoio/hugo/releases/tag/v0.126.2)
2. 将第一步下载的文件解压到所需安装的目录。例如本文将Hugo解压至```C:\softwares\hugo```目录下。
3. 将第二步中"hugo.exe"所在路径（即```C:\softwares\hugo```）添加至系统环境变量。
4. win+R，输入cmd，在命令行中输入```hugo version```，如果命令行输出以下内容则安装成功。

    ```hugo v0.126.2-8f3d902ce51512931f5759e9279d93e346c9e649+extended windows/amd64 BuildDate=2024-05-30T15:19:22Z VendorInfo=gohugoio```

## 第二步：创建站点
- 进入需要存放站点文件的目录。例如本文将站点存放于```C:\Users\xiong\Desktop\Hugo```，因此：
    1. 打开cmd进入站点存放目录：```cd C:\Users\xiong\Desktop\Hugo```
    2. 使用hugo创建新的站点：```hugo new site my_hugo_site```
    3. 进入新创建的站点目录：```cd my_hugo_site```
    4. 查看站点目录结构如下：```dir```
    ```bash
    ▸ archetypes/
    ▸ doc/content/
    ▸ data/
    ▸ layouts/
    ▸ static/
      config.toml
    ```

## 第三步：新建文章
1. 确保当前处于```my_hugo_site```站点目录下
2. 使用创建文章内容页面：```hugo new posts/about.md```
    > 默认情况下, 所有文章和页面均作为草稿创建. 如果想要渲染这些页面, 请从元数据中删除属性```draft: true```, 设置属性```draft: false```或者为```hugo```命令添加```-D/--buildDrafts```参数.
3. 使用任意编辑器打开```aboud.md```，写入一些内容：
    ```markdown
    🎉 欢迎来到我的个人博客！
    ```

## 第四步：安装主题
1. 本文以 [DoIt](https://github.com/HEIGE-PCloud/DoIt) 主题为例，安装其他主题可参考 [Hugo主题列表](https://www.gohugo.org/theme/)。
2. 确保当前处于```my_hugo_site```站点目录下，下载DoIt主题：```git clone https://github.com/HEIGE-PCloud/DoIt.git themes/DoIt```

## 第五步：运行调试
1. 修改 ```hugo.toml``` 文件为以下内容：
    ```text
    baseURL = "http://example.org/"
    # 设置默认的语言
    defaultContentLanguage = "zh-cn"
    # 网站语言
    languageCode = "zh-CN"
    # 是否包括中日韩文字
    hasCJKLanguage = true
    # 站点标题
    title = "博客熊"
    # 更改使用 Hugo 构建网站时使用的默认主题
    theme = "DoIt"
    ```
2. 确保当前处于```my_hugo_site```站点目录下，执行：```hugo server -D --disableFastRender```
    > 添加```--disableFastRender```参数来实时预览你正在编辑的文章页面.
