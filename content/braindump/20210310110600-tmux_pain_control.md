+++
title = "tmux-pain-control"
author = [7696122]
lastmod = 2022-01-19T10:43:34+09:00
tags = ["tpm", "tmux"]
draft = false
+++

```conf
set -g @plugin 'tmux-plugins/tmux-pain-control'

# {
# https://github.com/christoomey/vim-tmux-navigator
# is_vim="ps -o state= -o comm= -t '#{pane_tty}' \
# | grep -iqE '^[^TXZ ]+ +(\\S+\\/)?g?(view|n?vim?x?)(diff)?$'"
# https://github.com/laishulu/emacs-tmux-pane
# Smart pane switching with awareness of vim splits
is_vim_emacs='echo "#{pane_current_command}" | \
grep -iqE "((^|\/)g?(view|n?vim?x?)(diff)?$)|emacs"'

# https://www.reddit.com/r/tmux/comments/2fgmf7/os_x_problems_with_meta_keys_with_tmux_and_iterm2/
set -g terminal-overrides "*:kUP3=\e[1;9A,*:kDN3=\e[1;9B,*:kRIT3=\e[1;9C,*:kLFT3=\e[1;9D"

# tmux-pain-control.elisp {
# enable in root key table
bind -n M-h if-shell "$is_vim_emacs" "send-keys M-h" "select-pane -L"
bind -n M-j if-shell "$is_vim_emacs" "send-keys M-j" "select-pane -D"
bind -n M-k if-shell "$is_vim_emacs" "send-keys M-k" "select-pane -U"
bind -n M-l if-shell "$is_vim_emacs" "send-keys M-l" "select-pane -R"
# bind -n M-\ if-shell "$is_vim_emacs" "send-keys M-\\" "select-pane -l"

# enable in copy mode key table
bind -Tcopy-mode-vi C-h if-shell "$is_vim_emacs" "send-keys M-h" "select-pane -L"
bind -Tcopy-mode-vi C-j if-shell "$is_vim_emacs" "send-keys M-j" "select-pane -D"
bind -Tcopy-mode-vi C-k if-shell "$is_vim_emacs" "send-keys M-k" "select-pane -U"
bind -Tcopy-mode-vi C-l if-shell "$is_vim_emacs" "send-keys M-l" "select-pane -R"
# bind -Tcopy-mode-vi C-\ if-shell "$is_vim_emacs" "send-keys M-\\" "select-pane -l"
# }
# }

# navigate.elisp {
# bind -n C-h run "(tmux display-message -p '#{pane_current_command}' | grep -iqE '(^|\/)n?vim(diff)?$|emacs.*$' && tmux send-keys C-h) || tmux select-pane -L"
# bind -n C-j run "(tmux display-message -p '#{pane_current_command}' | grep -iqE '(^|\/)n?vim(diff)?$|emacs.*$' && tmux send-keys C-j) || tmux select-pane -D"
# bind -n C-k run "(tmux display-message -p '#{pane_current_command}' | grep -iqE '(^|\/)n?vim(diff)?$|emacs.*$' && tmux send-keys C-k) || tmux select-pane -U"
# bind -n C-l run "(tmux display-message -p '#{pane_current_command}' | grep -iqE '(^|\/)n?vim(diff)?$|emacs.*$' && tmux send-keys C-l) || tmux select-pane -R"
# }

bind '"' split-window -c "#{pane_current_path}"
bind % split-window -h -c "#{pane_current_path}"
bind c new-window -c "#{pane_current_path}"

# bind -n Cmd-j if-shell "$is_vim_emacs" "send-keys C-j" "resize-pane -D"
# bind -n Cmd-k if-shell "$is_vim_emacs" "send-keys C-k" "resize-pane -U"
# bind -n Cmd-h if-shell "$is_vim_emacs" "send-keys C-h" "resize-pane -L"
# bind -n Cmd-l if-shell "$is_vim_emacs" "send-keys C-l" "resize-pane -R"

```
