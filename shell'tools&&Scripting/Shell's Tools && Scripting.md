## 一、课后练习

（1） 阅读 ls 的命令然后按照以下的方式显示
- Includes all files, including hidden files
- Sizes are listed in human readable format (e.g. 454M instead of 454279954)
- Files are ordered by recency
- Output is colorized
这里是指列出所有的文件，并且按照时间进行排列，且输出有颜色
-a ： 显示隐藏文件  -l : 按照 list 显示   --color=always 显示颜色  -h : human-read 可读形式  -t : 按照时间进行  
**Answer**  :  `ls -al --color=always -h -t`

(2) Write bash functions `marco` and `polo` that do the following. Whenever you execute `marco` the current working directory should be saved in some manner, then when you execute `polo`, no matter what directory you are in, `polo` should `cd` you back to the directory where you executed `marco`. For ease of debugging you can write the code in a file `marco.sh` and (re)load the definitions to your shell by executing `source marco.sh`.

写 `marco 和 polo ` 两个函数。当运行`marco` 函数的时候，当前工作目录将会被保存下来，之后当你运行 `polo` 时，无论你在哪个目录，你都可以回到原来运行 `marco` 的目录。

这里首先 shell 编程中，第一行要加上  `#!/bin/bash` 这里是指定了 shell 脚本解释器的路径。不写会存在问题。
然后就是 shell 函数的规则，其实 shell 函数中有很多规则，列举一下：
1.关于参数：
`-$0 : 脚本名称
`-$1 ~ $9 ： 脚本的参数`
`-$@ : 显示所有参数`
`-$#  :  显示参数个数`
`-$$ : 显示当前脚本进程的 PID `
`!! : 显示最后一个命令以及结果`
`-$_ : 显示最后一个命令的参数`
2. 关于参数赋值：注意 `=` 旁边不能有空格
`ex : num = $(pwd) is wrong`
`ex : num=$(pwd) is true`
3. 函数之中也存在局部变量以及全局变量，要注意
**Answer ： 
```
#！/bin/bash
path=null
marco(){
	# 保存 pwd 路径
	path=$(pwd)
}

polo(){
	cd ${path}
}
```

(3) Say you have a command that fails rarely. In order to debug it you need to capture its output but it can be time consuming to get a failure run. Write a bash script that runs the following script until it fails and captures its standard output and error streams to files and prints everything at the end. Bonus points if you can also report how many runs it took for the script to fail.

有一个很少失败的脚本，为了进行 debug 需要捕获他的输出，但由于可能有时可能会失败。你需要写一个脚本，能够运行一下的脚本，在其失败的时候，获取标准输出以及错误流至文件中，且最后将一切打印。如果能够计算出，其运行多少次失败更好。

写这个我们首先需要明白，shell 脚本中语句运行的输入输出如何捕获，以及是如何定义的。
```
- 标准输入文件(stdin): stdin 的文件描述符为 0
- 标准输出文件(stdout): stdout 的文件描述符为 1
- 标准错误文件(stderr): stderr 的文件描述符为 2
Unix 会向以上三个文件中写入信息
```
下面还有一些使用示例
`ex : command 2> file  将 stderr 重定向到 file 中
**Answer**
```
#!/bin/bash
./count_random_test.sh
while [[ "$?" -ne 1 ]]; 
do
	count=$((count+1))
	./count_random_test.sh 1>>run.log 2>>error.log
done
echo "run ${count} and fail"
```

这里就是当运行存在错误的时候，就需要进行判断 [[shell脚本中的一些细节]] 。比如 `$?`  返回 0 时，表示运行正确，而在需要进行 `debug`  的脚本中，当出现错误时，返回状态为 1，故判断当 `$?` 返回值不为 1 时，脚本继续执行。

还有就是如何定位错误，上面讲到文件的标准输入输出以及标准错误，可以举个例子：`./test.sh 1>>run.log 2>>error.log`  这里就是将标准输出 `1` 和标准错误流 `2` 添加到 `run.log` 以及 `error.log` 文件中。

（4）As we covered in the lecture  `find`’s  `-exec` can be very powerful for performing operations over the files we are searching for. However, what if we want to do something with **all** the files, like creating a zip file? As you have seen so far commands will take input from both arguments and STDIN. When piping commands, we are connecting STDOUT to STDIN, but some commands like `tar` take inputs from arguments. To bridge this disconnect there’s the [`xargs`](https://www.man7.org/linux/man-pages/man1/xargs.1.html) command which will execute a command using STDIN as arguments. For example ` ls | xargs rm` will delete the files in the current directory.

Your task is to write a command that recursively finds all HTML files in the folder and makes a zip with them. Note that your command should work even if the files have spaces (hint: check `-d` flag for `xargs`).

我们可以看到 `find` 命令在我们进行文件查找的时候非常有效，但当需要队所有文件进行一些操作（如 zip）的时候。如你所见现在命令们将从标准输入以及参数中获取输入。当通过管道时，我们将输入与输出进行连接。但是像 `tar` 这样的命令以参数为输入的，为了连接这些，有一个 `xargs` 命令，可以将输出作为参数使用。如 `ls | xargs rm` 将会删除这个目录中的文件。

你的任务就是写一个命令，可以快速找到文件中的所有 `html` 文件并且将他们打包为 `zip`. 注意即使文件中有空格，命令也需要有效。（提示：检查 `-d` 的标志 `xargs`）

首先要思考 `find` 命令（即快速找到所有 .html 文件）

关于 `find` 命令：
```
# find . -name "*.html*"  // 将目录下所有 .html 文件列出来
# find . -type f          // 将目录下面所有文件列出来
```

关于 `xargs` 命令：
```
# xargs 可以捕获一个命令的输出，然后传递给另外一个命令
find . -name "*.html" | xargs ls -l 
# 这里就是将 html 文件按照 ls 列出来
```

关于 `zip` 命令：
```
# zip 命令:生成 zip 文件
zip html.zip /home/html
```

所以结合一下可以得出答案：
`find -name "*.html*" | xargs zip a  #找出所有的 html 文件生成一个 zip`  
``
(5) Write a command or script to recursively find the most recently modified file in a directory. More generally, can you list all files by recency?

写一个命令或者脚本，来找到目录中最晚修改的文件（以递归的方式）。或者说是，能将所有文件按照最新修改时间进行排列。

Answer : `find .-type f|xargs ls -c -lt`

![[Pasted image 20221206221112.png]]