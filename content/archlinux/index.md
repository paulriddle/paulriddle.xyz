+++
title = "Arch Linux notes"
template = "archlinux.html"
date = 2020-09-20
updated = 2020-09-20
[extra]
tags = []
+++

# Arch Linux notes

To update pacman mirrors:
```fish
cd /etc/pacman.d
sudo cp mirrorlist old_mirrorlist
sudo mv mirrorlist.pacnew mirrorlist
reflector --ipv6 --sort score -n 15 > ~/ml
sudo mv ~/ml mirrorlist
bat mirrorlist
diff old_mirrorlist mirrorlist
sudo rm old_mirrorlist # if the new one looks legit
```
Clean yay cache:
```
yay -Sc
```

---
