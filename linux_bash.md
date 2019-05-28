# Linux Bash 使用笔记 #

简单记录常用的 bash 命令。

- Windows:
- Linux  :

### `uname -a`

显示当前主机/远程机器的计算机名、系统信息；例如，下面是从 git-bash 中的测试结果：

    Mean@MEAN-PC /d/Develop/Git/repo/test (master)
    $ uname
    MINGW32_NT-6.1

    Mean@MEAN-PC /d/Develop/Git/repo/test (master)
    $ uname -a
    MINGW32_NT-6.1 VSENSE-PC 1.0.12(0.46/3/2) 2012-07-05 14:56 i686 unknown


### 清除屏幕

- Windows: `cls`；   # clear screen
- Linux  : `clear`；


### 创建目录

- Windows: `mkdir` 或者 `md`；
- Linux  : `mkdir`；


### 删除目录/文件

- Windows: `del <file>`
- Windows: `rmdir /s /q <path>` 或 `rd /s /q <path>`
- Linux  : `rm -rf <path>|<file>`


### 打印当前工作目录

- Linux  : `pwd`

### 切换当前工作目录

- Windows: `cd` 或者 `chdir` # `cd /d` 不仅可以切换当前磁盘驱动器下的目录，还可以直接在不同磁盘驱动器间切换；  
- Linux  : `cd` # 将目录切换到用户账户目录；（/c/Users/U10）
- Linux  : `cd /` # 将目录切换到初始工作目录 `/`,该目录其实是 git-bash.exe 所在目录位置；
- Linux  : `cd -` # 在最近的连个目录之间相互切换；

**提示**：Linux 下，如果 `cd` 后为空，则会将目录切换到用户账户目录；Windows 下，则什么也不做；

	U10@DESKTOP-EH2NLD2 MINGW32 /
	$ pwd
	/
	
	U10@DESKTOP-EH2NLD2 MINGW32 /
	$ cd d:
	
	U10@DESKTOP-EH2NLD2 MINGW32 /d
	$ cd
	
	U10@DESKTOP-EH2NLD2 MINGW32 ~
	$ pwd
	/c/Users/U10
	
	U10@DESKTOP-EH2NLD2 MINGW32 ~
	$ cd -
	/d
	
	U10@DESKTOP-EH2NLD2 MINGW32 /d
	$ cd -
	/c/Users/U10
	
	U10@DESKTOP-EH2NLD2 MINGW32 ~
	$ cd /
	
	U10@DESKTOP-EH2NLD2 MINGW32 /
	$ pwd
	/
	
	U10@DESKTOP-EH2NLD2 MINGW32 /
	$

### 打印文件内容

- Windows: `type`；	# 例如： `type readme.txt`
- Linux  : `cat`；	# 例如： `cat readme.txt`

### `ls` 命令

ls -l .git
ls -l .git/objects/

[Linux 中的 15 个基本 ‘ls’ 命令示例](https://linux.cn/article-5109-1.html)

### 文件查找 `find`

find .git/objects/ -type f

[Linux 文件查找工具之 find “大宝剑”](http://blog.jobbole.com/100925/)


