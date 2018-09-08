# Mac

1. brew 和 brew cask

    ```
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```
2. node
    1. nvm
    2. npm
3. Python
    1. pyenv
    2. pip
4. ruby
    1. rvm
    2. gem
5. 调试命令
    1. strace：Linux下用来跟踪某个进程的系统调用，在mac下可以使用`dtruss`
    2. ldd：在Linux下用来查看某个elf文件依赖的动态链接库，在mac下可以用`otool`
6. Java相关设置
7. 定制提示符

    ```
    export PS1="[\[\e[36;1m\]\u@\[\e[32;1m\]\H \[\e[34;1m\]\W\[\e[0m\]] #"
    ```
8. 屏幕截图
    1. Cmd + Shift + 3:  截屏
    2. Cmd + Shift + 4： 选定区域截屏
    3. Cmd + Shift + 4 + Space: 选定窗口截屏
9. 隐藏文件
    1. 隐藏

        ```
        defaults write com.apple.finder AppleShowAllFiles -bool false
        ```
    2. 显示

        ```
        defaults write com.apple.finder AppleShowAllFiles -bool true
        ```
10. 清空域名解析缓存

    ```
    sudo killall -HUP mDNSResponder
    ```
