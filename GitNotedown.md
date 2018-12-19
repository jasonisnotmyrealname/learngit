git廖雪峰的教程非常不错
工作区是一个仓库，工作区下面的.git文件（隐藏文件）是一个版本库，包括stage暂存区和master主分支
git add xxx，将xxx放到暂存区。可以连续add多个文件。如果不add到暂存区，commit时是不会提交这次变化的。
git commit -m "comment content"    将stage中的内容提交到master分支,并写下这次提交的评论
git reset --hard HEAD^  版本回退到HEAD之前的分支(再之前的是HEAD^^，再之前的HEAD)。HEAD是master中的版本指针.
git reset --hard xxxxx  版本回退（也可以前进到删除过的版本）到xxxx，其中xxx是版本号，是一个很长的hash值。这个值可以在git reflog中翻到历史，可以在git log查到当前各个状态的各个commit版本值。
git reset HEAD "filename". 将暂存区的文件回退到工作区。即如果使用git status查看暂存区内有文件，会同时提示：“使用 "git reset HEAD <文件>..." 以取消暂存”
git checkout -- "filename" 丢弃工作区filename的修改
撤销更改的总结
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

git status 查看git的状态：是否有新建文件、修改文件;上述文件是否需要add到暂存器。该命令同时可以查看当前分支。
git log:查看每次commit的记录，按Q退出。
git reflog:查看每次指令记录
git diff查看尚未暂存的文件更新了哪些部分
git diff --staged查看已经暂存起来的文件(staged)和上次提交时的快照之间(HEAD)的差异
git diff HEAD 显示工作版本(Working tree)和HEAD的差别
git diff SHA1 SHA2 比较两个历史版本之间的差异
git branch xxx（在master下执行） 新建一个xxx的分支
git checkout master/xxx 转换分支
git merge xxx(在master分支下执行) 合并xxx分支到master
git branch 查看当前分支
git branch -d b2(在master下执行)，删除b2分支（合并之后可能就没有用了）
git log --graph：可以看到分支图

# git stash 
保存当前分支
意义：如果在分支dev1上工作，没有add和commit，直接切换到master上，会发现改动的内容直接体现在master的文件上，所以需要stash一下工作分支，然后新建bug分支开始新的工作。采用stash多个分支可以切换分支工作而不影响其他分支工作。

# git stash list
看stash列表

# git stash pop
将stash保存的分支pop出来，在修改完master的bug之后可以继续工作了。

# git push
向远程推送

# git pull
从远程下载

# git submodule
开发过程中，经常会有一些通用的部分希望抽取出来做成一个公共库来提供给别的工程来使用，而公共代码库的版本管理是个麻烦的事情。今天无意中发现了git的git submodule命令，之前的问题迎刃而解了。

- 添加
为当前工程添加submodule，命令如下：
```
git submodule add 仓库地址 路径
```
比如 git submodule add https://github.com/mavlink/c_library_v2.git mavlink/include/mavlink/v2.0
其中，“仓库地址”是指子模块仓库地址，“路径”指将子模块放置在当前工程下的路径。 
注意：路径不能以 / 结尾（会造成修改不生效）、不能是现有工程已有的目录（不能順利 Clone）
命令执行完成，会在当前工程根路径下生成一个名为__“.gitmodules”的文件__，其中记录了子模块的信息。添加完成以后，再将子模块所在的文件夹添加到工程中即可。

- 删除
submodule的删除稍微麻烦点：首先，要在“.gitmodules”文件中删除相应配置信息。然后，执行“git rm –cached ”命令将子模块所在的文件从git中删除。

- 下载的工程带有submodule
当使用git clone下来的工程中带有submodule时，初始的时候，submodule的内容并不会自动下载下来的，此时，只需执行如下命令：
git submodule update --init --recursive
即可将子模块内容下载下来后工程才不会缺少相应的文件。

- 更新.gitsubmodule中的remote url
git submodule sync --recursive --quiet 

在px4使用git submodule sync和git submodule update语句后并make，console打印出：
子模组 'mavlink/include/mavlink/v2.0' (https://github.com/mavlink/c_library_v2.git) 未对路径 '../../../mavlink/include/mavlink/v2.0' 注册
子模组路径 '../../../mavlink/include/mavlink/v2.0'：检出 'd8fcf0a694dc11b3f83b89a0970e3d8c4e48d418'

PX4/Firmware下面的.gitmodules文件，该文件记录了所有的submodule：
[submodule "mavlink/include/mavlink/v2.0"]
	path = mavlink/include/mavlink/v2.0
	url = https://github.com/mavlink/c_library_v2.git
	branch = master
[submodule "src/modules/uavcan/libuavcan"]
	path = src/modules/uavcan/libuavcan
	url = https://github.com/UAVCAN/libuavcan.git
	branch = master
[submodule "msg/tools/genmsg"]
	path = msg/tools/genmsg
	url = https://github.com/ros/genmsg.git
	branch = indigo-devel
[submodule "msg/tools/gencpp"]
	path = msg/tools/gencpp
	url = https://github.com/ros/gencpp.git
	branch = indigo-devel
[submodule "Tools/jMAVSim"]
	path = Tools/jMAVSim
	url = https://github.com/PX4/jMAVSim.git
	branch = master
[submodule "Tools/sitl_gazebo"]
	path = Tools/sitl_gazebo
	url = https://github.com/PX4/sitl_gazebo.git
	branch = master
[submodule "src/lib/matrix"]
	path = src/lib/matrix
	url = https://github.com/PX4/Matrix.git
	branch = master
[submodule "src/lib/DriverFramework"]
	path = src/lib/DriverFramework
	url = https://github.com/PX4/DriverFramework.git
	branch = master
[submodule "src/lib/ecl"]
	path = src/lib/ecl
	url = https://github.com/PX4/ecl.git
	branch = master
[submodule "cmake/cmake_hexagon"]
	path = cmake/cmake_hexagon
	url = https://github.com/ATLFlight/cmake_hexagon.git
	branch = master
[submodule "src/drivers/gps/devices"]
	path = src/drivers/gps/devices
	url = https://github.com/PX4/GpsDrivers.git
	branch = master
[submodule "src/modules/micrortps_bridge/micro-CDR"]
	path = src/modules/micrortps_bridge/micro-CDR
	url = https://github.com/eProsima/micro-CDR.git
	branch = master
[submodule "platforms/nuttx/NuttX/nuttx"]
	path = platforms/nuttx/NuttX/nuttx
	url = https://github.com/PX4-NuttX/nuttx.git
	branch = px4_firmware_nuttx-7.22+
[submodule "platforms/nuttx/NuttX/apps"]
	path = platforms/nuttx/NuttX/apps
	url = https://github.com/PX4-NuttX/apps.git
	branch = px4_firmware_nuttx-7.22+
[submodule "cmake/configs/uavcan_board_ident"]
	path = cmake/configs/uavcan_board_ident
	url = https://github.com/PX4/uavcan_board_ident.git
	branch = master

- 测试代码
执行：
git submodule add https://github.com/PX4/GpsDrivers.git src/drivers/gps/devices
git submodule add https://github.com/mavlink/c_library_v2.git mavlink/include/mavlink/v2.0
执行看到文件夹下多了一个.gitsubmodule文件：
[submodule "mavlink/include/mavlink/v2.0"]
	path = mavlink/include/mavlink/v2.0
	url = https://github.com/mavlink/c_library_v2.git
[submodule "src/drivers/gps/devices"]
	path = src/drivers/gps/devices
	url = https://github.com/PX4/GpsDrivers.git
可以手动删除mavlink/include/mavlink/v2.0和src/drivers/gps/devices目录下的所有文件（包括.git)，然后再执行：
git submodule update --init --recursive
就可以从git中恢复（更新）所有的文件


# orgin和master：
在clone完成之后，Git 会自动为你将此远程仓库命名为origin（origin只相当于一个别名，运行git remote –v或者查看.git/config可以看到origin的含义），并下载其中所有的数据，建立一个指向它的master 分支的指针，我们用(远程仓库名)/(分支名) 这样的形式表示远程分支，所以origin/master指向的是一个remote branch（从那个branch我们clone数据到本地），但你无法在本地更改其数据。

# git init  和 git init –bare 的区别
使用命令"git init --bare"初始化的版本库(暂且称为bare repository)只会生成一类文件:用于记录版本库历史记录的.git目录;而不会包含实际项目源文件的拷贝。
比如在project目录下执行git init --bare project只会创建一个project.git的文件夹。且该在project下，不能使用git指令。再git clone project.git project1，进入到project1后，git status，会显示：“位于分支master，您的分支与上游分支'origin/master'一致。”再git remote -v，会显示fetch和push的origin的路径都是project.git。
如果使用git init，会生成一个.git的文件夹。可以使用git指令，比如git status，会显示“位于分支master。”

# 本地删除文件,git远程不同步删除
# add时用git add .


