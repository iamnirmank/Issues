## Uninstall Any App
```
sudo apt purge app_name
sudo apt autoremove
```
## Create a virtual screen to split the screen
```
xrandr --fb 1366x1536 --output eDP-1 --mode 1366x768 --panning 683x768+0+0 --scale 1x1 --output eDP-1 --panning 683x768+683+0 --right-of eDP-1
```
## Undo the Configuration for the virtual screen
```
xrandr --output eDP-1 --auto
```

