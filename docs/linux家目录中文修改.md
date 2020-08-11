# 修改home目录下文件夹为英文

## 方法一

修改家目录下配置文件`~/.config/user-dirs.dirs`

内容如下：

```shell
XDG_DESKTOP_DIR="$HOME/Desktop"
XDG_DOWNLOAD_DIR="$HOME/Downloads"
XDG_TEMPLATES_DIR="$HOME/Templates"
XDG_PUBLICSHARE_DIR="$HOME/Public"
XDG_DOCUMENTS_DIR="$HOME/Documents"
XDG_MUSIC_DIR="$HOME/Music"
XDG_PICTURES_DIR="$HOME/Pictures"
XDG_VIDEOS_DIR="$HOME/Videos"
```

## 方法二

修改环境变量

```shell
export LANG=en_US
```

使用更新命令

```shell
xdg-user-dirs-gtk-update
```

重启系统后会提示是否更改回中文，选择不再提示并取消更改