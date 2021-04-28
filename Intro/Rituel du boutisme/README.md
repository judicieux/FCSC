# Rituel du boutisme
Ce challenge était sûrement un des plus simples.<br/>
## Explication
Avant-tout, une brève explication du concept "Rituel du boutisme".<br/>
Quand certains ordinateurs enregistrent un entier sur 32 bits en mémoire, par exemple 0xA0B70708 en notation hexadécimale, ils l'enregistrent dans des octets dans l'ordre qui suit : A0 B7 07 08, pour une structure de mémoire fondée sur une unité atomique de 1 octet et un incrément d'adresse de 1 octet. Ainsi, l'octet de poids le plus fort (ici A0) est enregistré à l'adresse mémoire la plus petite, l'octet de poids inférieur (ici B7) est enregistré à l'adresse mémoire suivante et ainsi de suite. voir <a href="https://fr.wikipedia.org/wiki/Boutisme"/>Lien</a><br/>
## Solution
Quand on fait un file disque.img on obtient ceci:<br/>
```
g3zb0yy@FCSC#~/Bureau $ file disque.img 
disque.img: Linux rev 1.0 ext4 filesystem data, UUID=b10781e9-d553-4053-bb93-064cdc03ed6b (extents) (64bit) (large files) (huge files)
```
Rien de plus basique, c'est tout simplement une image de partition ext4.<br/>
En prêtant attention aux paranthèses "(extents) (64bit) (large files) (huge files)" je comprends vite que l'image est corrupted.<br/>
Malgré tout je décide tout de même de monter l'image, même si je m'attends à avoir des problèmes de montage.<br/>
### Mounting image
Tout d'abord je check les devices loop free.<br/>
```
g3zb0yy@FCSC#~/Bureau $ losetup -a
/dev/loop1: []: (/var/lib/snapd/snaps/core20_904.snap)
/dev/loop19: []: (/home/hyp0/Bureau/fcsc/forensic/disque.img)
/dev/loop17: []: (/var/lib/snapd/snaps/telegram-desktop_2610.snap)
/dev/loop8: []: (/var/lib/snapd/snaps/ngrok_29.snap)
/dev/loop15: []: (/var/lib/snapd/snaps/spotify_46.snap)
/dev/loop6: []: (/var/lib/snapd/snaps/gtk-common-themes_1514.snap)
/dev/loop13: []: (/var/lib/snapd/snaps/snapd_11588.snap)
/dev/loop4: []: (/var/lib/snapd/snaps/gnome-3-34-1804_36.snap)
/dev/loop11: []: (/var/lib/snapd/snaps/snap-store_518.snap)
/dev/loop2: []: (/var/lib/snapd/snaps/core18_1997.snap)
/dev/loop0: []: (/var/lib/snapd/snaps/core18_1988.snap)
/dev/loop18: []: (/var/lib/snapd/snaps/telegram-desktop_2619.snap)
/dev/loop9: []: (/var/lib/snapd/snaps/snap-store_467.snap)
/dev/loop16: []: (/var/lib/snapd/snaps/sublime-text_97.snap)
/dev/loop7: []: (/var/lib/snapd/snaps/gtk-common-themes_1515.snap)
/dev/loop14: []: (/var/lib/snapd/snaps/spotify_45.snap)
/dev/loop5: []: (/var/lib/snapd/snaps/gnome-3-34-1804_66.snap)
/dev/loop12: []: (/var/lib/snapd/snaps/snapd_11402.snap)
/dev/loop3: []: (/var/lib/snapd/snaps/core20_975.snap)
/dev/loop10: []: (/var/lib/snapd/snaps/ngrok_32.snap)
```
Toutes les loop sont utilisés, je crée donc une nouvelle lib avec mknod.<br/>
```
g3zb0yy@FCSC#~/Bureau $ sudo mknod -m640 /dev/loop30 b 7 8

```
``-m640`` définit la permission du device.
``/dev/loop30`` définit le nom du device.
``b`` pour la création du special block device.
``7 8`` le nombre 7 & 8 définissent le MAJOR & MINOR
