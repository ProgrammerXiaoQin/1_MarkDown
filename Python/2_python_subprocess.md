#python #subprocess
subprocess是Python 2.4中新增的一个模块，它允许你生成新的进程，连接到它们的 input/output/error 管道，并获取它们的返回（状态）码。可以用于执行系统命令

### 1.常用函数
1. `subprocess.run(args, *, stdin=None, input=None, stdout=None, stderr=None, shell=False, timeout=None, check=False, universal_newlines=False)`
>Python 3.5中新增的函数。执行指定的命令，等待命令执行完成后返回一个包含执行结果的CompletedProcess类的实例。

2. `subprocess.call(args, *, stdin=None, stdout=None, stderr=None, shell=False, timeout=None)`
>执行指定的命令，返回命令执行状态，类似于os.system(cmd),0为执行成功。

3. `subprocess.check_output(args, *, stdin=None, stderr=None, shell=False, universal_newlines=False, timeout=None)`
>Python 2.5中新增的函数。 执行指定的命令，如果执行成功则返回状态码，否则抛出异常。其功能等价于subprocess.run(..., check=True)。

### 2.参数说明
- **args：** 需要执行的命令,可以是个字符串或者字符串序列,需要设置shell为True

- **shell：** 如果shell为True，那么指定的命令将通过shell执行。

- **check：** 如果check参数的值是True，且执行命令的进程以非0状态码退出，则会抛出一个CalledProcessError的异常，且该异常对象会包含 参数、退出状态码、以及stdout和stderr(如果它们有被捕获的话)。

- **stdout, stderr：input：** 该参数是传递给Popen.communicate()，通常该参数的值是一个字节序列，如果universal_newlines=True，其值是一个字符串。
    - run()不会捕获命令执行结果的stdout和stderr，如果我们向获取这些内容需要传递subprocess.PIPE，然后可以通过返回的CompletedProcess类实例的stdout和stderr属性或捕获相应的内容；
    - call()和check_call()函数返回的是命令执行的状态码，而不是CompletedProcess类实例 , stdout和stderr不适合赋值为subprocess.PIPE；
    - check_output()函数默认就会返回命令执行结果，所以不用设置stdout的值，如果我们希望在结果中捕获错误信息，可以执行stderr=subprocess.STDOUT。

- **universal_newlines：** 该参数影响的是输入与输出的数据格式，当它的值默认为False，此时stdout和stderr的输出是字节序列；当该参数的值设置为True时，stdout和stderr的输出是字符串。

### 3.subprocess.CompletedProcess类介绍
- **args：** 用于加载该进程的参数，这可能是一个列表或一个字符串

- **returncode：** 子进程的退出状态码。通常情况下，退出状态码为0则表示进程成功运行了；一个负值-N表示这个子进程被信号N终止

- **stdout：** 从子进程捕获的stdout。如果run()函数被调用时指定stderr=subprocess.STDOUT，那么stdout和stderr将会被整合到这一个属性中，且stderr将会为None

- **stderr：** 从子进程捕获的stderr。它的值与stdout一样，是一个字节序列或一个字符串。如果stderr没有被捕获的话，它的值就为None

- **check_returncode()：** 如果returncode是一个非0值，则该方法会抛出一个CalledProcessError异常。

### 4.实例
```python
import subprocess  
from subprocess import PIPE 

# 在shell中执行ping www.baidu.com命令,在命令执行完后输出该命令执行过程中的        # stdout(P.stdout)和命令执行结果(P.returncode)
def test():  
    P = subprocess.run("ping www.baidu.com",shell=True,stdout=PIPE,stderr=PIPE,universal_newlines=True)  
    print(P.stdout)
    print(P.returncode)
```