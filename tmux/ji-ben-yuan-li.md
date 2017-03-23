tmux的主要元素分为三层：
- Session 一组窗口的集合，通常用来概括同一个任务。session可以有自己的名字便于任务之间的切换。
- Window 单个可见窗口。Windows有自己的编号，也可以认为和ITerm2中的Tab类似。
- Pane 窗格，被划分成小块的窗口，类似于Vim中 C-w +v 后的效果。
![tmux](/assets/tmux.png)

Session总在tmux的左下角显示，Window也会在最下方以一种Tab的形式展现，Pane则是在Window中的窗口。Session和Window都可以重命名。

基本操作
Tmux的快捷键，都必须以前置操作开始。tmux默认的前置操作是CTRL+b。例如，我们想要新建一个窗体，就需要先在键盘上摁下CTRL+b，松开后再摁下n键。
