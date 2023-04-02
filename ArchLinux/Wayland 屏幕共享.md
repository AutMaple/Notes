# 开启远程共享

```shell
sudo groupadd  pipewire
sudo usermod -aG pipewire username
sudo mv /etc/pipewire /etc/pipewire.bak
sudo pacman -S pipewire pipewire-media-session gst-plugin-pipewire
sudo reboot now
```

执行完上边的脚本之后还需要开启 `gnome-remote-desktop` 服务以及在 `设置中心` 的 `共享` 选项中开启对应的选项

## 参考文章

	- https://forum.manjaro.org/t/gnome-remote-desktop-blank-screen/122345