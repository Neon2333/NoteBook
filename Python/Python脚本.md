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

## 2. bad interpreter: /usr/bin/python3^M: 没有那个文件或目录

报错：zsh: ./Bluetooth-DOS-Attack.py: bad interpreter: /usr/bin/python3^M: 没有那个文件或目录

原因：脚本是在Windows下写的，换行符为\r\n，而Unix下是\n，多了一个\r。

使用vim修改：

```bash
#首先使用vim查看文件格式
vim xxx.sh
:set ff
#可以看到文件格式为
fileformat=dos
#修改文件格式
:set ff=unix
#保存退出
:wq
```

## 3. Missing parentheses in call to 'print'. Did you mean print(...)?

print ""是python2的print语法。

修改代码，或改解释器为Python3.





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
##直接把命令字符串写在Popen中（推荐使用）
res=subprocess.Popen('ls -al',stdout=subprocess.PIPE,shell=True)

out=res.stdout.read()
res.stdout.close()
print(out.decode('utf-8').strip())
```

```python
# 用户输入
s.stdin.write(b"import os\n")
```

## 3.获取Popen输出

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
sudo apt install gnome-terminal
```

### （1）exec bash和bash区别

`exec bash`和`bash`主要区别就是前者会开新bash替换原bash。

```bash
exec bash	# 在当前bash下开新bash替代当前bash（pid变了）

exec bash -c "ping www.baidu.com"	#新bash下执行了命令，命令执行完新bash会退出（原bash被替换了）

gnome-terminal -- /usr/bin/bash -c 'exec bash'
# 在当前焦点的bash下开新的bash窗口，原bash窗口不会被关闭，并将焦点在新bash窗口下，直到新bash被关闭。
```

```bash
bash	#在当前bash下开新的子shell。若执行了命令或脚本，执行完后子shell将自动终止
bash -c "ping www .baidu.com"
bash demo.sh
```

### （2）打开新终端执行命令后新终端保留

```bash
# 打开新终端并执行命令
# 休眠5s
# 不exec bash开的新bash会自动终止（命令执行完了）
# exec bash在新bash-ver1.0的基础上开新bash-ver2.0
gnome-terminal -- /bin/bash -c 'pgrep bash;sleep 3;exec bash'
```

### （3）打开新终端执行不结束命令固定时间后新终端保留

```bash
gnome-terminal -- /bin/bash -c 'timeout 10s ping www.baidu.com;sleep 3;exec bash'
```

### （4）打开新终端执行命令后新终端退出

```bash
gnome-terminal -- /bin/bash -c 'python3 --version'
```

```python
#! /usr/bin/python3

import subprocess
def run_command(command):
    cmd="gnome-terminal -- /bin/bash -c '" + command +"'"
    subprocess.Popen(cmd,stdout=subprocess.PIPE,stderr=None,shell=True)	
	out=res.stdout.read()
	res.stdout.close()
run_command("ls -al")
```

### （5）打开新终端执行不结束命令固定时间后新终端退出

```bash
gnome-terminal -- /bin/bash -c 'timeout 10s ping www.baidu.com'
```

### （6）循环执行，每次执行完一次后新终端退出

```python
# python
while True:
    subprocess.Popen("gnome-terminal -- /bin/bash -c 'timeout 10s ping www.baidu.com'",shell=True)
    time.sleep(10+5)	#新终端退出后sleep 5s再开新终端
```

### （7）开新终端循环执行，新终端不退出





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

## 7. 程序结束前执行某些操作

Python的标准库中，有一个atexit模块专门用于注册在程序退出前执行的函数。

```python
import atexit

def save_data():
    # 保存数据的逻辑
    print("正在保存数据...")

atexit.register(save_data)

# 程序逻辑
print("程序运行中...")
```

定义了一个 `save_data` 函数来表示保存数据的逻辑。然后，我们使用 `atexit.register` 方法将该函数注册为程序退出前要执行的函数。当程序退出时，`save_data` 函数将被调用，执行数据保存操作。



