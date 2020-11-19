# 在 Linux 使用 C++

- [在 Linux 使用 C++](#在-linux-使用-c)
  - [前提条件](#前提条件)
  - [检查 GCC 是否安装](#检查-gcc-是否安装)
  - [创建 Hello World 项目](#创建-hello-world-项目)
    - [添加 hellor world 源码文件](#添加-hellor-world-源码文件)
  - [探索 IntelliSense](#探索-intellisense)
  - [构建 helloworld.cpp](#构建-helloworldcpp)
    - [tasks.json 文件](#tasksjson-文件)
    - [运行构建](#运行构建)
    - [修改 tasks.json](#修改-tasksjson)
  - [调试 helloworld.cpp](#调试-helloworldcpp)
    - [launch.json 文件](#launchjson-文件)
    - [开始一个调试会话](#开始一个调试会话)
  - [单步执行代码](#单步执行代码)
  - [设置一个监视](#设置一个监视)
  - [C/C++ 配置](#cc-配置)
  - [重用 C++ 配置](#重用-c-配置)
  - [问题](#问题)
    - [编译器和链接错误](#编译器和链接错误)
  - [参考](#参考)

## 前提条件

1. 安装 [VSCode](https://code.visualstudio.com/download)
2. 安装 [C++ 插件](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

## 检查 GCC 是否安装

```sh
kiki@ubuntu:~/github/vscode-debug$ gcc -v
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-linux-gnu/5/lto-wrapper
Target: x86_64-linux-gnu
Configured with: ../src/configure -v --with-pkgversion='Ubuntu 5.4.0-6ubuntu1~16.04.12' --with-bugurl=file:///usr/share/doc/gcc-5/README.Bugs --enable-languages=c,ada,c++,java,go,d,fortran,objc,obj-c++ --prefix=/usr --program-suffix=-5 --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --with-sysroot=/ --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-gnu-unique-object --disable-vtable-verify --enable-libmpx --enable-plugin --with-system-zlib --disable-browser-plugin --enable-java-awt=gtk --enable-gtk-cairo --with-java-home=/usr/lib/jvm/java-1.5.0-gcj-5-amd64/jre --enable-java-home --with-jvm-root-dir=/usr/lib/jvm/java-1.5.0-gcj-5-amd64 --with-jvm-jar-dir=/usr/lib/jvm-exports/java-1.5.0-gcj-5-amd64 --with-arch-directory=amd64 --with-ecj-jar=/usr/share/java/eclipse-ecj.jar --enable-objc-gc --enable-multiarch --disable-werror --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu
Thread model: posix
gcc version 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.12)
# 如果没有安装使用下面的命令
# sudo apt-get update && sudo apt-get install -y build-essential gdb
```

## 创建 Hello World 项目

创建一个空文件夹，并在其中生成一个 `.vscode` 目录，在该目录下创建三个文件：

- `.vscode/task.json`: 编译器编译设置
- `.vscode/launch.json`: 调试器设置
- `.vscode/c_cpp_properties.json`: 编译器路径和 IntelliSense 设置

**在 VSCode 打开当前空文件夹。**

### 添加 hellor world 源码文件

在与上述 `.vscode` 同级目录创建 `helloworld.cpp` 源码文件：

```cpp
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

## 探索 IntelliSense

支持自动补全功能。

## 构建 helloworld.cpp

### tasks.json 文件

创建 `tasks.json` 文件，告诉 VSCode 如何构建(编译)程序。这个任务会调用 g++ 编译器从源码创建一个可执行文件。

确保 `helloworld.cpp` 已经在编辑器打开。

从主菜单，选择 `Terminal` > `Configure Default Build Task`。下拉菜单中有许多为 C++ 编译器预定义的构建任务。选择 `C/C++: g++ build active file`。

这会在 `.vscode` 目录创建一个 `tasks.json` 文件，并在编辑器打开此文件。`tasks.json` 文件应该看起来像下面这样：

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "type": "shell",
      "label": "build hello wolrd", //"g++ build active file"
      "command": "/usr/bin/g++",
      "args": ["-std=c++11", "-g", "${file}", "-o", "${fileDirname}/${fileBasenameNoExtension}"], //add -std=c++11
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

可以参考[变量参考](https://code.visualstudio.com/docs/editor/variables-reference)学习更多关于 `tasks.json`。

- `command` 指定运行的程序，这里就是 `g++`
- `args` 指定传递给 g++ 的命令行参数。这些参数必须按照编译器希望的顺序指定
  - 这个任务告诉 g++ 选择活跃的文件(`$file`)，编译并在当前目录(`${fileDirname}`)创建一个和文件同名且不带后缀的可执行文件(`${fileBasenameNoExtension}`)，这里就是 `helloworld`
- `label` 是在任务列表看到名字，可以按照自己的喜好定
- `group` 对象的 `"isDefault": true` 指定当你按下 `Ctrl+Shift+B` 时会运行这个任务
  - 这个属性只用于方便；如果设为 `false`，仍然可以在 `Terminal` 菜单使用 `Tasks: Run Build Task` 运行

### 运行构建

1. 返回 `helloworld.cpp`，这个是构建任务选择的活跃文件
2. 按下 `Ctrl+Shift+B` 或从 `Terminal` 菜单使用 `Tasks: Run Build Task` 运行预定义的构建任务
3. 当任务开始时，可以看到集成的终端面包出现在源码编辑器下面。任务完成时，终端显示编译器的输出，表示构建成功或失败。对于一个成功的 g++ 构建，输出应该类型下面的：

    ```txt
    > Executing task: /usr/bin/g++ -std=c++11 -g /home/kiki/github/vscode-debug/cppdebug/helloworld/helloworld.cpp -o /home/kiki/github/vscode-debug/cppdebug/helloworld/helloworld <


    Terminal will be reused by tasks, press any key to close it.
    ```

4. 创建一个新终端，在当前目录可以看到一个可执行文件 `helloworld`
5. 可以输入 `./helloworld` 运行

### 修改 tasks.json

可以修改 `tasks.json` 以构建多个 C++ 文件，使用一个类似 `"${workspaceFolder}/*.cpp"` 参数而不是 `${file}`。也可以通过替换 `${fileDirname}/${fileBasenameNoExtension}` 修改输出文件名，可以使用硬编码的文件名(比如 `helloworld.out`)。

## 调试 helloworld.cpp

### launch.json 文件

创建 `launch.json` 文件配置 VSCode 在按下 `F5` 时运行 GDB 调试器调试程序。

从主菜单，选择 `Run` > `Add Configuration...`，然后选择 `C++ (GDB/LLDB)`。

然后会在下拉菜单看到许多预定义的调试配置。选择 `g++ build and debug active file`。

VSCode 创建一个 `launch.json` 文件，在编辑器打开此文件，构建和运行 `helloworld`。

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "g++ - Build and debug active file",
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
            "preLaunchTask": "build hello wolrd",
            "miDebuggerPath": "/usr/bin/gdb"
        }
    ]
}
```

- `program` 指定要调试的程序。这里设置为活跃的文件夹 `${fileDirname}` 和活跃的不带后缀的文件名 `${fileBasenameNoExtension}`，即如果 `helloworld.cpp` 是活跃文件，那么该值就是 `helloworld`
- `stopAtEntry` 为 `false` 表示 C++ 插件不会再源码中添加任何断点，这是默认的

### 开始一个调试会话

将 `stopAtEntry` 修改为 `true`，会导致调试器开始调试的时候停在 `main` 函数。

1. 返回 `helloworld.cpp`，以便它成为活跃文件
2. 按下 `F5` 或者从主菜单选中 `Run` > `Start Debugging`。在开始进入代码之前，先花时间注意用户界面的一些改变：

   - 继承的终端出现在源码编辑器的下方。在 `Debug Output` 页，可以看到输出表示调试器已经开启并在运行
   - 编辑器高亮 `main` 方法的第一句。这是 C++ 插件自动设置的断点
   - 左边的 `Run` 视图展示调试信息
   - 在代码编辑器的顶部，出现一个调试控制面板。可以选中左边的点将其在屏幕中移动

## 单步执行代码

## 设置一个监视

## C/C++ 配置

如果想要对 C/C++ 插件控制更多。可以创建一个 `c_cpp_properties.json` 文件，该文件允许修改比如编译器路径、包含路径、C++ 标准(默认是 C++17) 等设置。

可以从命令面板 `Ctrl+Shift+P` 运行命令 `C/C++: Edit Configurations (UI)` 查看 C/C++ 配置 UI。这会打开 ``C/C++ Configurations` 页面。当在这里修改时，VSCode 会写入到 `.vscode` 目录下的 `c_cpp_properties.json` 文件。

如果程序头文件包含不在工作区或者标准库路径，才需要修改 `Include path`。

VSCode 将这些设置放在 `.vscode/c_cpp_properties.json`。如果直接打开文件，看起来类似：

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
            "cStandard": "gnu11",
            "cppStandard": "c++17",
            "intelliSenseMode": "gcc-x64"
        }
    ],
    "version": 4
}
```

## 重用 C++ 配置

VSCode 现在配置使用 Linux 的 gcc。配置应用到当前工作区。为了重用配置，将该 JSON 文件拷贝到一个新项目目录(工作区)的 `.vscode` 并按需修改源文件和可执行文件的名字。

## 问题

### 编译器和链接错误

当开始构建或调试时 `helloworld.cpp` 不是活跃文件，最常出现错误原因(比如 `undefined _main` 或 `attempting to link with file built for unknown-unsupported file format` 等)。这是因为编译器尝试编译的不是源码，比如 `launch.json`、`tasks.json`、`c_cpp_properties.json` 文件。

## 参考

- <https://code.visualstudio.com/docs/cpp/config-linux>
