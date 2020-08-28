vim /etc/pacman.conf

add

```csharp
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```

```bash
sudo pacman -Syy
sudo pacman -S archlinuxcn-keyring  
```