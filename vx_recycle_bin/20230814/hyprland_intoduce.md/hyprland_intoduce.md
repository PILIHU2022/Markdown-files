# 安装Hyprland
* WM: Hypland
* OS: Arch Linux
## 由于我使用的是NVIDIA显卡，并且安装了N卡驱动(nvidia-dkms)，所以需要安装hyprland-nvidia-git^AUR^，不安装git版本会导致某些功能不可用，作者就是这样改了半天:(

### 需要安装的软件包:
```
paru -S grim slurp hyprland-nvidia-git waybar-git kitty rofi dunst wl-clipboard cliphist wl-clip-persist ranger qt5-base qt5-wayland qt6-base qt6-wayland wofi
```

#Clipboard setting
* wl-clipboard: 提供 wayland 剪贴板支持
    * 添加至exec_once.conf
        * `exec-once = wl-paste --type text --watch cliphist store   #Stores only text data` `exec-once = wl-paste --type image --watch cliphist store  #Stores only image data`
* cliphist: 支持文本和图片的剪贴板包装应用
    * 添加至keybinds.conf
        * `bind = SUPER, V, exec, cliphist list | wofi --dmenu | cliphist decode | wl-copy` 
* wl-clip-persist: 长时间保存剪贴板数据
    * exec-once 
        * `exec-once = wl-clip-persist --clipboard both              # Use Regular and Primary clipboard,long :w`
