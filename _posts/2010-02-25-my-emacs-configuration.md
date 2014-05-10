---
layout: post
title: "my emacs configuration"
categories: "学习&实践&思考"
---

I used to use Eclipse and later Netbeans for development, 1 years ago switched to Emacs.

It was a big pain to learn and config Emacs at beginning, then I fall in love with it.

Here is part of my configuration. Visit [here](https://github.com/zhangzhe/g_emacs "here") for more information. Hope it helps:)


**cheat sheet**

- C-v                     Scroll down (toward end of buffer)
- M-v                     Scroll up (toward beginning of buffer)
- C-u #                   Prefix numeric arg # to next cmd
- C-g                     Stop a command in progress
- C-x C-c                 Exit emacs
- C-a                     move-beginning-of-line
- C-e                     move-end-of-line
- C-l                     recenter-top-bottom
- C-n                     next-line
- C-r                     isearch-backward
- C-s                     isearch-forward
- C-up                    backward-paragraph
- C-down                  next-paragraph
- C-x C-f                 find-file
- C-x C-w                 write-file
- C-x 1                   delete-other-windows
- C-x 2                   split-window-vertically
- C-x 3                   split-window-horizontally
- C-x s                   save-some-buffers
- M-<                     beginning-of-buffer
- M-=                     count-lines-region
- M->                     end-of-buffer
- <f1> k                  Info-goto-emacs-key-command-node
- <f1> w                  where-is


**self config cheat sheet**

- C-c C-c C-s             tags-search
- C-c C-c C-n             tags-loop-continue
- C-c C-c C-c             inari-find-controller
- C-c C-c C-m             rinari-find-model
- C-c C-c C-v             rinari-find-view
- C-c C-c C-r             rinari-find-rspec
- C-c C-c C-f             rinari-find-file-in-project
- C-c C-c C-h             hide-all-blocks
- C-c C-c C-t             toggle-hiding-block
- C-c C-c C-o             comment-region
- C-c C-c C-u             uncomment-region
- C-c C-c C-g             goto-line
- <f5>                    smart-compile
- <f8>                    ecb-toggle-ecb-windows
- <f12>                   kill-this-buffer
- C-<f12>                 kill-all-rinari-buffers
