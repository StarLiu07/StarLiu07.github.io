---
title: vscode配置Cpp
date: 2022-07-24 21:52:15
tags:
- C++
categories:
- 教程
---

**聊一聊**

对于我这个萌新来说，想要在vscode上优雅的写代码，但是又苦于不会配置每次弄都会出这样那样的问题。不过vscode有一个孪生兄弟——vs。vs这个设计就很贴心了啊，你只管写代码，剩下的交给IDE（￣︶￣）。但是对于配置低的电脑跑起来还是有点吃力的。于是我寻思着一定要好好研究一下配置vscode。

## 一.添加环境变量

要编写代码肯定需要对应的编译器，而vscode并不会先给你安装好，这就需要我们再另外下载编译器。这里推荐使用[MinGW](https://sourceforge.net/projects/mingw-w64/files/)(It's free o(￣▽￣)ｄ)

### 1.添加

下载完成后把bin文件夹的完整路径添加到环境变量中，依次打开  **设置->系统->关于->高级系统设置->环境变量->找到系统变量中的Path->点击编辑->新建->把刚才bin的完整路径粘贴进去**

{% asset_img 添加路径.png 添加路径 %}

### 2.检查是否添加成功

使用快捷键 win + r  然后输入cmd,接着再黑窗窗中输入

> gcc -v

{% asset_img 检查版本.png 检查版本 %}

如果出现了一大堆东西的话就说明添加成功了

## 二.下载插件

vscode只是一个纯文本的**编辑器**，不比其他的IDE，它更加趋近于“模块化”，想要实现啥功能就得下载对应的插件，所以说插件就是vscode的灵魂所在。

那么编写C++代码并运行需要那些插件呢？

### 1.C/C++

{% asset_img C++.png C/C++ %}

这个拓展主要就是提供Debug和Format的功能。

### 2.Code Runner

{% asset_img CodeRunner.png CodeRunner %}

![image-20220724224331374](vscode配置C/image-20220724224331374.png)

这个拓展可以运行大部分的编程语言，并且右键就可以运行代码很方便。

### 3.其他插件

##### ①Chinese language pack

{% asset_img 中文.png Chinese language pack %}

这个插件主要是把你的vscode都变成简体中文，毕竟作为一个中国人，页面都是英文都是看的不舒服(虽然看上去很牛逼\(￣︶￣*\)))

##### ②Bracket Pair Color DLW

{% asset_img 彩虹括号.png 彩虹括号 %}

这个插件又名彩虹括号，这样子的话你写的代码就会有很多不同颜色的括号。虽然没啥用，但是优雅！

## 三.配置.json文件

首先创建一个打算存放代码的文件夹(由于vscode本身的原因，路径和文件夹名称都**不能有中文**)，接着在vscode中打开这个文件夹。打开之后再创建名为**.vscode**的文件夹，再在这个文件夹下创建 **launch.json**  **tasks.json**  **settings.json**.接下来再把下面的代码一次复制进去就好了

### 1.launch.json代码

```json
// https://code.visualstudio.com/docs/cpp/launch-json-reference
{
    "version": "0.2.0",
    "configurations": [{
        "name": "(gdb) Launch", // 配置名称，将会在启动配置的下拉菜单中显示
        "type": "cppdbg", // 配置类型，对于C/C++可认为此处只能是cppdbg，由cpptools提供；不同编程语言不同
        "request": "launch", // 可以为launch（启动）或attach（附加）
        "program": "D:\\VsCode\\CppCode\\exe\\${fileBasenameNoExtension}.exe", // 将要进行调试的程序的路径
        "args": [], // 程序调试时传递给程序的命令行参数，一般设为空
        "stopAtEntry": false, // 设为true时程序将暂停在程序入口处，相当于在main上打断点
        "cwd": "${workspaceFolder}", // 调试程序时的工作目录，此为工作区文件夹；改成${fileDirname}可变为文件所在目录
        "environment": [], // 环境变量
        "externalConsole": true, // 使用单独的cmd窗口，与其它IDE一致；为false时使用内置终端
        "internalConsoleOptions": "neverOpen", // 如果不设为neverOpen，调试时会跳到“调试控制台”选项卡，你应该不需要对gdb手动输命令吧？
        "MIMode": "gdb", // 指定连接的调试器，可以为gdb或lldb。但我没试过lldb
        "miDebuggerPath": "gdb.exe", // 调试器路径，Windows下后缀不能省略，Linux下则不要
        "setupCommands": [
            { // 模板自带，好像可以更好地显示STL容器的内容，具体作用自行Google
                "description": "Enable pretty-printing for gdb",
                "text": "-enable-pretty-printing",
                "ignoreFailures": false
            }
        ],
        "preLaunchTask": "Compile" // 调试前执行的任务，一般为编译程序。与tasks.json的label相对应
    }]
}
```

### 2.tasks.json代码

这里要注意一下，如果是用vscode编译C++代码则要把编译器改成g++

```json
// https://code.visualstudio.com/docs/editor/tasks
{
    "version": "2.0.0",
    "tasks": [{
        "label": "Compile", // 任务名称，与launch.json的preLaunchTask相对应
        "command": "g++",   // 要使用的编译器，C++用g++
        "args": [
            "${file}",
            "-o",    // 指定输出文件名，不加该参数则默认输出a.exe，Linux下默认a.out
            "D:/VsCode/CppCode/exe/${fileBasenameNoExtension}.exe",
            "-g",    // 生成和调试有关的信息
            "-m64",  // 不知为何有时会生成16位程序而无法运行，此条可强制生成64位的
            "-Wall", // 开启额外警告
            "-static-libgcc",     // 静态链接libgcc，一般都会加上
            "-fexec-charset=GBK", // 生成的程序使用GBK编码，不加这条会导致Win下输出中文乱码；繁体系统改成BIG5
            "-D__USE_MINGW_ANSI_STDIO", // 用MinGW写C时留着，否则不需要，用于支持printf的%zd和%Lf等
        ], // 编译的命令，其实相当于VSC帮你在终端中输了这些东西
        "type": "process", // process是把预定义变量和转义解析后直接全部传给command；shell相当于先打开shell再输入命令，所以args还会经过shell再解析一遍
        "group": {
            "kind": "build",
            "isDefault": true // 不为true时ctrl shift B就要手动选择了
        },
        "presentation": {
            "echo": true,
            "reveal": "always", // 执行任务时是否跳转到终端面板，可以为always，silent，never。具体参见VSC的文档，即使设为never，手动点进去还是可以看到
            "focus": false,     // 设为true后可以使执行task时焦点聚集在终端，但对编译C/C++来说，设为true没有意义
            "panel": "shared"   // 不同的文件的编译信息共享一个终端面板
        },
        "problemMatcher":"$gcc" // 捕捉编译时终端里的报错信息到问题面板中，修改代码后需要重新编译才会再次触发
        // 本来有Lint，再开problemMatcher就有双重报错，但MinGW的Lint效果实在太差了；用Clangd可以注释掉
    }]
}
```

### 3.settings.json代码

```json
{
    "files.defaultLanguage": "c", // ctrl+N新建文件后默认的语言
    "editor.formatOnType": true,  // 输入分号(C/C++的语句结束标识)后自动格式化当前这一行的代码
    "editor.suggest.snippetsPreventQuickSuggestions": false, // clangd的snippets有很多的跳转点，不用这个就必须手动触发Intellisense了
    "editor.acceptSuggestionOnEnter": "off", // 我个人的习惯，按回车时一定是真正的换行，只有tab才会接受Intellisense
    // "editor.snippetSuggestions": "top", // （可选）snippets显示在补全列表顶端，默认是inline

    "code-runner.runInTerminal": true, // 设置成false会在“输出”中输出，无法输入
    "code-runner.executorMap": {
        "c": "gcc '$fileName' -o '$fileNameWithoutExt.exe' -Wall -O2 -m64 -lm -static-libgcc -fexec-charset=GBK -D__USE_MINGW_ANSI_STDIO && &'./$fileNameWithoutExt.exe'",
        "cpp": "g++ '$fileName' -o '$fileNameWithoutExt.exe' -Wall -O2 -m64 -static-libgcc -fexec-charset=GBK && &'./$fileNameWithoutExt.exe'"
        // "c": "gcc $fileName -o $fileNameWithoutExt.exe -Wall -O2 -m64 -lm -static-libgcc -fexec-charset=GBK -D__USE_MINGW_ANSI_STDIO && $dir$fileNameWithoutExt.exe",
        // "cpp": "g++ $fileName -o $fileNameWithoutExt.exe -Wall -O2 -m64 -static-libgcc -fexec-charset=GBK && $dir$fileNameWithoutExt.exe"
    }, // 右键run code时运行的命令；未注释的仅适用于PowerShell（Win10默认）和pwsh，文件名中有空格也可以编译运行；注释掉的适用于cmd（win7默认）、PS和bash，但文件名中有空格时无法运行
    "code-runner.saveFileBeforeRun": true, // run code前保存
    "code-runner.preserveFocus": true,     // 若为false，run code后光标会聚焦到终端上。如果需要频繁输入数据可设为false
    "code-runner.clearPreviousOutput": false, // 每次run code前清空属于code runner的终端消息，默认false
    "code-runner.ignoreSelection": true,   // 默认为false，效果是鼠标选中一块代码后可以单独执行，但C是编译型语言，不适合这样用
    "code-runner.fileDirectoryAsCwd": true, // 将code runner终端的工作目录切换到文件目录再运行，对依赖cwd的程序产生影响；如果为false，executorMap要加cd $dir

    "C_Cpp.clang_format_sortIncludes": true, // 格式化时调整include的顺序（按字母排序）
}
```

## 四.其他配置

### 1.保存文件

做完上面的步骤配置基本算结束了，但是还有一个小问题就是每次写代码的时候都需要打开刚才我们创建的.vscode的文件，这就很麻烦。解决这个问题有两个方法

1.每次创建新文件夹的时候把那几个json文件复制进去

2.把工作区路径传给vscode主程序

第一个太简单了，而且第二个弄好后可以轻松点所以就演示下第二种方法。

首先创建快捷方式，然后把工作区路径作为参数传给vscode(这里记得打引号)

{% asset_img 保存文件.png 保存文件 %}

在目标里填vscode的路径和你开始创建的文件夹的路径，比如上图的路径为"D:\VsCode\Microsoft VS Code\Code.exe" "D:\VsCode\CppCode"

那么如果你全部按上述步骤做完了的话那应该是没啥大问题了，以后再遇到了问题再补充。
