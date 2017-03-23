## 1. 配置文件
每当开启一个新的会话时，Tmux 都会先读取 ~/.tmux.conf 这个文件。该文件中存放的就是对 Tmux 的配置

## 2. 重新载入配置
按下 Ctrl-b r 就可以重新加载配置并使新的配置生效
```
# bind a reload key
bind R source-file ~/.tmux.conf ; display-message "Config reloaded.."
```
## 3. 变更前置操作
将快捷键前缀变更为 Ctrl-a
```
unbind C-b
set -g prefix C-a
```

## 4. 把 Tmux 设为 vi 模式
```
setw -g mode-keys vi
```

## 5. 设置分pane和分windows时自动使用当前目录
```
# tmux 1.8
set -g default-path "~"
bind c set default-path "" \; new-window \; set -u default-path
bind % set default-path "" \; split-window -h \; set -u default-path
bind '"' set default-path "" \; split-window -v \; set -u default-path

# tmux 1.9 (not check)
bind c new-window -c '#{pane_current_path}'
bind % split-window -h -c '#{pane_current_path}'
bind '"' split-window -v -c '#{pane_current_path}'
```
