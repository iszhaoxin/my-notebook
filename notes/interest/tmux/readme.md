1. tmux source-file .tmux.conf 

   这个用于进入tmux后 source 设置

   位置在 ~/.tmux.conf 

2. 设置

   ```
    unbind C-b
    set-option -g prefix C-a
    bind-key C-a send-prefix
    bind | split-window -h
    bind - split-window -v
    unbind '"'
    unbind %
    bind r source-file ~/.tmux.conf
    bind -n M-Left select-pane -L
    bind -n M-Right select-pane -R
    bind -n M-Up select-pane -U
    bind -n M-Down select-pane -D
    set-option -g allow-rename off
   ```

   这些就够用了

   ​