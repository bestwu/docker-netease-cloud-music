# 准备工作

允许所有用户访问X11服务,运行命令:

```bash
    xhost +
```

## 查看系统audio gid

```bash
  cat /etc/group | grep audio
```

fedora 26 结果：

```bash
audio:x:63:
```

## 运行

### docker-compose

```yml
version: '2'
services:
  music:
    image: bestwu/netease-cloud-music
    container_name: music
    privileged: true
    devices:
      - /dev/snd
    volumes:
      - /tmp/.X11-unix:/tmp/.X11-unix
      - $HOME/Music:/home/netease/Music
    environment:
      - DISPLAY=unix$DISPLAY
      - QT_IM_MODULE=fcitx
      - XMODIFIERS=@im=fcitx
      - GTK_IM_MODULE=fcitx
      - AUDIO_GID=63 # 可选 默认63（fedora） 主机audio gid 解决声音设备访问权限问题
      - GID=1000 # 可选 默认1000 主机当前用户 gid 解决挂载目录访问权限问题
      - UID=1000 # 可选 默认1000 主机当前用户 uid 解决挂载目录访问权限问题
    # command: --no-sandbox #如果不想设置privileged: true，可使用此参数以no-sandbox 方式运行
```
或

```bash
    docker run -d --name music --device /dev/snd --privileged=true\
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -v $HOME/Music:/home/netease/Music \
    -e DISPLAY=unix$DISPLAY \
    -e XMODIFIERS=@im=fcitx \
    -e QT_IM_MODULE=fcitx \
    -e GTK_IM_MODULE=fcitx \
    -e AUDIO_GID=`getent group audio | cut -d: -f3` \
    -e GID=`id -g` \
    -e GID=`id -g` \
    bestwu/netease-cloud-music
```