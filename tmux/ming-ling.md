查看配置
tmux show -g


Session相关操作（除非显式地关闭会话，否则 Tmux 的会话在重启计算机之前都不会消失）

| 命令     | 解释     |
|:---      | :---    |
| `tmux ls`         | 查看当前的tmux服务中有哪些Session
| `tmux a [-t sess]`| 还没有运行 Tmux，接入已开启的会话
| `prefix s`        | 查看/切换session
| `prefix d`        | 离开Session
| `prefix $`        | 重命名当前Session

创建新Session
- 没有进入tmux时：
tmux new -s <name>
- 已经在tmux中：
prefix : 然后输入new -s <name>

Window相关操作
新建窗口
prefix c
切换到上一个活动的窗口
prefix space
关闭一个窗口
prefix &
使用窗口号切换
prefix 窗口号
重命名
prefix ,<newname>
prefix :rename-window <newname>
但是创建window或者pane的时候会重新命名当前窗口的名称，在~/.tmux.conf中加上: set-option -g allow-rename off

Pane相关操作
切换到下一个窗格
prefix o
查看所有窗格的编号
prefix q
垂直拆分出一个新窗格
prefix “
水平拆分出一个新窗格
prefix %
暂时把一个窗体放到最大
prefix z

选择和复制内容
prefix [ 进入复制模式，然后可以看到一小段高亮的文本出现在了屏幕的右上角 [0/98]
接下来就可以像在 Vim 中一样用 j、k、l 和 h 等键在文本间移动光标了。也可以用鼠标移动。
把光标移动到想复制的文本上后再按下空格键就可以开始选择文本了（这和在 Vim 中复制文本的步骤一模一样）。<使用时没有复现>
选择完要复制的文本后再按下回车键。


