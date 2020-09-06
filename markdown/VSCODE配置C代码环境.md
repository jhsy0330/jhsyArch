# Arch使用vs code编译调试C/C++

Windows平台下无脑下一步编译器就安装好了，转到linux平台下基本没有无脑下一步这种操作，这个时候就需要手动配置相关程序IDE+GCC，去年有过一次手动安装gcc和依赖的一次经历，时隔太久已经记不太清步骤，这次略过了gcc的配置过程，因为base-devel已经包含gcc。只配置了vs，下次有需求的时候再写一下配置gcc的过程

### 安装VScode

安装code

sudo pacman -S code

### VS插件

首次打开VS后会提示直接打开插件安装界面，如果需要中文直接搜索language找到简体中文安装语言包。

### 安装gcc和标准库

在安装arch的时候基本都会选择安装base-devel包，已经包含了gcc和glibc只需要安装调试程序GDB即可

编译C语言程序需要gcc编译，直接安装gcc和C库glibc

sudo pacman -S gcc glibc

### Hello World

建立工作目录并且打开VS

```
mkdir projects
cd projects
mkdir helloworld
cd helloworld
code
```

在工作目录下会默认生成.vscode文件夹，包含相关配置文件

* task.json （编译打包设置）
* launch.json （debug设置）
* c_cpp_properties.json （编译路径和补全设置）

创建第一个helloworld.cpp文件

```C++
#include <iostream>
#include <vector>
#include <string>

using namespace std;

int main()
{
    vector<string> msg {"Hello", "C++", "World", "from", "VS Code", "and the C++ extension!"};

    for (const string& word : msg)
    {
        cout << word << " ";
    }
    cout << endl;
}
```

Ctrl+S 保存文件

Ctrl+Shift+E 在左侧文件浏览器中定位文件

### 编译hello world

接下来需要创建一个tasks.json配置文件告诉VS如何编译一个程序，上文提到的.vsconde配置下会创建一个tasks.json配置文件

在菜单栏**终端（Terminal）**按钮，选择 **配置默认生成程序（Configure Default Build Task）**在弹出菜单中选择**C/C++: g++ build active file**

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "type": "shell",
      "label": "g++ build active file",
      "command": "/usr/bin/g++",
      "args": ["-g", "${file}", "-o", "${fileDirname}/${fileBasenameNoExtension}"],
      "options": {
        "cwd": "/usr/bin"
      },
      "problemMatcher": ["$gcc"],
      "group": {
        "kind": "build",
        "isDefault": true
      }
    }
  ]
}
```

更多配置文件信息https://code.visualstudio.com/docs/editor/variables-reference

以上配置信息清晰明了，直译就是参数的作用。就是VS帮你调用g++编译你的cpp文件

### 运行编译程序

选择你要编译的文件，点击菜单栏**终端（Terminal）**>>**运行生成任务（Run Build Task）**

```
> Executing task: /usr/bin/gcc -g /home/jhsy/code/helloworld.cpp -o /home/jhsy/code/helloworld <


终端将被任务重用，按任意键关闭。
```

此时已经编译完成，直接可以在终端运行./helloworld

### Debug helloworld.cpp

要运行debug的话需要配置launch.json运行GDB debugger

在主菜单上 选择**运行（Run）**> **添加配置 （Add Configuration）** 选择**C++（GDB/LLDB）**

在弹出菜单中选择**g++ - 生成和调试活动文件**

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "g++ build and debug active file",
      "type": "cppdbg",
      "request": "launch",
      "program": "${fileDirname}/${fileBasenameNoExtension}",
      "args": [],
      "stopAtEntry": false,
      "cwd": "${workspaceFolder}",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        }
      ],
      "preLaunchTask": "g++ build active file",
      "miDebuggerPath": "/usr/bin/gdb"
    }
  ]
}
```

生成如上launch.json调试配置文件

### 开始调试程序

返回helloworld.cpp文件

此时左边资源管理器已经关闭的话按Ctrl+Shift+E打开资源管理器

按下F5或者在主菜单的**运行（Run）** > **启用调试（Start Debugging）**

## C/C++ 配置

通过创建c_cpp_properties.json文件控制C/C++拓展插件

按下Ctrl+Shitf+P输入**C/C++: Edit Configurations (UI)**编辑配置文件

```json
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [],
            "compilerPath": "/usr/bin/gcc",
            "cStandard": "gnu18",
            "cppStandard": "gnu++14",
            "intelliSenseMode": "gcc-x64"
        }
    ],
    "version": 4
}
```

reference:

​	Visual Studio Code Guide https://code.visualstudio.com/docs/cpp/config-linux