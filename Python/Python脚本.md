## 1. Linux环境执行脚本

```python
进入到脚本所在的路径直接使用如下命令
python3 test.py 
```

```python
在脚本前加上解释器路径：
#!/usr/bin/python3
# -*- coding:utf-8 -*-

直接运行该脚本文件，不需要使用Python环境变量命令即可
./test.py
```

## 2. subprocess模块

***可执行bash命令或bash脚本。***

subprocess 模块替代了一些老的模块和函数，**比如：`os.system`**

subprocess 过去版本中的`call()`，`check_call()`和`check_output()`已经被`run()`方法取代了。`run()`方法为 3.5 版本新增。

大多数情况下，推荐使用`run()`方法调用子进程，执行操作系统命令。

在更高级的使用场景，你还可以使用 Popen 接口。其实`run()`方法在底层调用的就是 Popen 接口。

**用Popen()。**

### 1）subprocess.run

**功能：执行 args 参数所表示的命令，等待命令结束，并返回一个 CompletedProcess 类型对象。**

```python
subprocess.run(args, *, stdin=None, input=None, stdout=None, stderr=None, shell=False, timeout=None, check=False, encoding=None, errors=None)
```

* 参数列表：

  **args**：表示要执行的命令，必须是一个字符串，字符串参数列表。

  **stdin、stdout 和 stderr**：子进程的标准输入、输出和错误。其值可以是`subprocess.PIPE`、`subprocess.DEVNULL`、一个已经存在的文件描述符、已经打开的文件对象或者 None。

  **shell：Linux 中，当 args 是个字符串时（不要用变量赋值字符串，直接把字符串写到Popen中），请设置 shell=True，当 args 是个列表的时候，shell 保持默认的 False。**

  **timeout**：设置命令超时时间。如果命令执行时间超时，子进程将被杀死，并弹出`TimeoutExpired`异常。

* 返回值：

  run() 方法返回的是一个 **CompletedProcess 类型对象ret**，不能直接获取我们通常想要的结果。要获取命令执行的结果或者信息，在调用 run() 方法的时候，请指定 stdout=subprocess.PIPE。会将结果以 bytes 类型保存在 ret.stdout 属性中，通过ret.stdout获取。

  如果不设置`stdout=subprocess.PIPE`，那么在返回值`CompletedProcess(args='ls -al', returncode=0)`中不会包含 stdout 属性

  ```python
  #! /usr/bin/python3
  
  import subprocess
  
  mycmd=["ls", "-la"]
  try:
  	res=subprocess.run(args=mycmd,stdout=subprocess.PIPE,shell=False);
  	print(res.stdout)
  except subprocess.CalledProcessError as e:
      print("错误信息:\n")
      print(e.output.decode('utf-8').strip())
  ```

### 2）subprocess.Popen()（推荐使用）

用法和参数与run()方法、参数列表基本类同，但是它的返回值是一个Popen对象，而不是`CompletedProcess`对象。

Popen对象的stdin、stdout和stderr是三个文件句柄，可以像文件那样进行读写操作。

可以方便的实现执行交互式命令（并不是所有的操作系统命令都像‘dir’或者‘ipconfig’那样单纯地返回执行结果，你要输入点什么，然后它返回执行的结果）。

**通过`s.stdin.write()`可以输入数据，而`s.stdout.read()`则能输出数据。**

```python
#! /usr/bin/python3

import subprocess
##使用列表形式的命令
mycmd=["ls", "-la"]
res=subprocess.Popen(args=mycmd,shell=False,stdout=subprocess.PIPE)
##直接把命令字符串卸载Popen中（推荐使用）
res=subprocess.Popen('ls -al',stdout=subprocess.PIPE,shell=False)

out=res.stdout.read()
res.stdout.close()
print(out.decode('utf-8').strip())
```

```python
# 用户输入
s.stdin.write(b"import os\n")
```

# 3.获取Popen输出

```python
#stdout=subprocess.PIPE参数表示将命令的输出重定向到一个管道中。然后使用communicate()方法获取进程的输出结果。
import subprocess

# 执行命令，获取输出结果
proc = subprocess.Popen(['ls', '-l'], stdout=subprocess.PIPE)
output = proc.communicate()[0]

# 输出结果
print(output.strip())#去掉换行符
```

```python
import subprocess

proc = subprocess.Popen(['ls', '-l'], stdout=subprocess.PIPE)

# 非阻塞方式获取输出结果
while proc.stdout.readable():
    output = proc.stdout.read(1)
    if not output:
        break
    print(output.decode('utf-8').strip(), end='')
```





## 4. 打开新终端

```python
#! /usr/bin/python3

import subprocess
cmd=["gnome-terminal"]
res=subprocess.Popen(cmd)
```

## 5. 打开新终端并在新终端下执行命令

https://blog.csdn.net/fengqianlang/article/details/131190515

```bash
# 打开新终端并执行bash
gnome-terminal -- /bin/bash -c 'python3 --version;sleep 5;exec bash'
#-e在Ubuntu下报错option “-e” is deprecated and might be removed in a later version of gnome-terminal.
# -e改成--还是报错
```

```python
#! /usr/bin/python3

import subprocess
def run_command(command):
    cmd="gnome-terminal -- /bin/bash -c '" + command + ";exec bash'"
    subprocess.Popen(cmd,stdout=subprocess.PIPE,stderr=None,shell=True)	
	out=res.stdout.read()
	res.stdout.close()
run_command("ls -al")
```

## 6. 调用c#程序

```python
import subprocess

# 启动 C# 应用程序

process = subprocess.Popen(["C:\\path\\to\\your\\executable.exe"])

# 等待应用程序退出

process.wait()

# 检查退出码

if process.returncode == 0:

    print("C# 应用程序成功退出")

else:

    print("C# 应用程序退出时发生错误")

运行这个 Python 脚本，它将启动 C# 应用程序，并在应用程序退出后打印相应的消息。
```





