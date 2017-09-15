Set of commands to setup raspberry kiosk mode

# Setup instructions

- burn image
    ```
    diskutil list
    diskutil unmountDisk /dev/diskX
    sudo dd bs=1m conv=sync if=/Users/user/Movies/2017-07-05-raspbian-jessie.img of=/dev/rdiskX
    diskutil eject /dev/diskX
    ```
- put `wpa_supplicant.conf` into `boot` partitition
- put empty `ssh` file into `boot` partitition


# Configure raspi

## connect
```
ssh root@raspberrypi.local
```

## change default password
```
passwd
```

## disable overscan
```
sudo raspi-config
```

## change wallpaper

- open config file
    ```
    sudo nano /home/pi/.config/pcmanfm/LXDE-pi/desktop-items-0.conf
    ```
- change lines
    ```
    wallpaper_mode=color
    desktop_bg=#000000
    show_trash=0
    ```
- restart pcmanfm
    ```
    DISPLAY=:0 pcmanfm --reconfigure
    ```


## remove top panel

- open config file
    ```
    sudo nano /home/pi/.config/lxpanel/LXDE-pi/panels/panel
    ```
- change lines
    ```
    autohide=1
    heightwhenhidden=0
    ```
- reboot `sudo reboot`


## setup power managment

- open
    ```
    sudo nano /etc/lightdm/lightdm.conf
    ```
- add
    ```
    xserver-command=X -s 0 dpms
    ```

<!-- - open
    ```
    sudo nano /etc/kbd/config
    ```
- set
    ```
    BLANK_TIME=0
    ``` -->

<!-- - open
    ```
    sudo nano /etc/xdg/lxsession/LXDE-pi/autostart
    ```
- replace the whole file
    ```
@lxpanel --profile LXDE-pi
@pcmanfm --desktop --profile LXDE-pi
#@xscreensaver -no-splash
@point-rpi
@xset s off
@xset -dpms
@xset s noblank
@sed -i 's/"exited_cleanly": false/"exited_cleanly": true/' ~/.config/chromium/Default/Preferences
    ``` -->


## autohide cursor

- install
    ```
    sudo apt-get install x11-xserver-utils unclutter
    ```
- open
    ```
    sudo nano /etc/xdg/lxsession/LXDE/autostart
    ```
- add line
    ```
    @unclutter -idle 0.1 -root
    ```


## install node
```
wget -O - https://raw.githubusercontent.com/sdesalas/node-pi-zero/master/install-node-v8.4.0.sh | bash
```

### Add support for node CLI tools
```
# Add the following to the end of your ~/.profile file:
export PATH=$PATH:/opt/nodejs/bin
```


## setup a tunnel
- generate ssh keys, [instruction](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md)
    ```
    sudo ssh-keygen -t rsa -C eben@pi
    ```
- copy key over
    ```
    sudo cat /root/.ssh/id_rsa.pub
    ```
- add your key to `authorized_keys`
    ```
    sudo nano /root/.ssh/authorized_keys
    ```
- install autossh
    ```
    sudo apt-get update && sudo apt-get install autossh
    ```
- add cron job (`crontab -e`)
    ```
    @reboot /usr/bin/autossh -Tqf -o "ServerAliveInterval=5" -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no root@diane.ai -NC -R "*:5555:localhost:22"
    ```


## add chromium autostart to crontab
    ```
    @reboot tmux new -d 'sleep 30 && DISPLAY=:0 /usr/bin/chromium-browser --noerordialogs --incognito --disable-infobars --kiosk "http://bemyfriend.online/screens/1"'
    ```



## start a video

```
omxplayer `youtube-dl -g https://www.youtube.com/watch?v=bCOc8IS0Uq8` --win "100 80 1380 800"
vlc `youtube-dl -g https://www.youtube.com/watch?v=bCOc8IS0Uq8`
```


## start browser

```
DISPLAY=:0 /usr/bin/chromium-browser --noerordialogs --incognito --disable-infobars --kiosk "https://www.youtube.com/watch?v=ekjibsihUBo"
DISPLAY=:2 /usr/bin/chromium-browser --noerordialogs --incognito --disable-infobars --kiosk "file:///home/pi/ireland/Petr%20Fetisov_ZagranPass.pdf"
```


## take screenshot
```
sudo apt-get install scrot
DISPLAY=:0 scrot
```


## Configure cron

```
#@reboot /usr/bin/autossh -Tqf -o "ServerAliveInterval=5" -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no root@diane.ai -NC -R "*:5556:localhost:5901"
@reboot tmux new -d 'omxplayer `youtube-dl -g https://www.youtube.com/watch?v=q6EoRBvdVPQ` --win "100 80 1380 800"'

#@reboot tmux new -d 'sleep 30 && DISPLAY=:0 /usr/bin/chromium-browser --noerordialogs --incognito --disable-infobars --kiosk "http://192.168.43.193:6100"'

@reboot tmux new -d 'sleep 30 && DISPLAY=:0 /usr/bin/chromium-browser --noerordialogs --incognito --disable-infobars --kiosk "http://bemyfriend.online/screens/1"'

@reboot /usr/bin/autossh -Tqf -o "ServerAliveInterval=5" -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no root@diane.ai -NC -R "*:5555:localhost:22"
@reboot /usr/bin/node /root/connect-to-wifi.js >> /root/connect-to-wifi.log
```

