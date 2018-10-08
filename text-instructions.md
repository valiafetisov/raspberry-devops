Set of commands to setup Raspberry PI kiosk mode.

# Prepare SD card

- Burn image
    ```
    diskutil list
    diskutil unmountDisk /dev/diskX
    sudo dd bs=1m conv=sync if=~/2017-07-05-raspbian-jessie.img of=/dev/rdiskX
    diskutil eject /dev/diskX
    ```
- Put `wpa_supplicant.conf` into `boot` partitition with such a sintax
    ```
    network={
      ssid="WIFI name"
      psk="password"
      key_mgmt=WPA-PSK
      priority=10
    }
    ```
- Put empty `ssh` file into `boot` partitition


# Configure Raspberry PI

## Connect
```
ssh root@raspberrypi.local
```


## Change default password
```
passwd
```


## Disable overscan
```
sudo raspi-config
```


## Change wallpaper

- Open config file
    ```
    sudo nano /home/pi/.config/pcmanfm/LXDE-pi/desktop-items-0.conf
    ```
- Change lines
    ```
    wallpaper_mode=color
    desktop_bg=#000000
    show_trash=0
    ```
- Restart pcmanfm
    ```
    DISPLAY=:0 pcmanfm --reconfigure
    ```


## Remove top (task) panel

- Open config file
    ```
    sudo nano /home/pi/.config/lxpanel/LXDE-pi/panels/panel
    ```
- Change lines
    ```
    autohide=1
    heightwhenhidden=0
    ```
- Reboot `sudo reboot`


## Setup power managment

- Open
    ```
    sudo nano /etc/lightdm/lightdm.conf
    ```
- Replace
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


## Autohide cursor

- Install
    ```
    sudo apt-get install x11-xserver-utils unclutter
    ```
- Open
    ```
    sudo nano /etc/xdg/lxsession/LXDE/autostart
    ```
- Add line
    ```
    @unclutter -idle 0.1 -root
    ```

## Configure Cron to start a browser

Run `crontab -e` and paste:

```
@reboot tmux new -d 'sleep 30 && DISPLAY=:0 /usr/bin/chromium-browser --noerordialogs --incognito --disable-infobars --kiosk "http://bemyfriend.online/screens/1"'
```

__Configure Cron from root to take care of broken browser sessions:__

Run `sudo crontab -e` and paste:

```
@reboot /bin/rm /home/pi/.config/chromium/SingletonLock
@reboot /bin/sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' /home/pi/.config/chromium/Default/Preferences
```


# Extra

## Install Node
```
wget -O - https://raw.githubusercontent.com/sdesalas/node-pi-zero/master/install-node-v8.4.0.sh | bash
```

__Add support for Node CLI tools__
```
# Add the following to the end of your ~/.profile file:
export PATH=$PATH:/opt/nodejs/bin
```


## Setup a reverse tunnel to your server (optional)

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

# Useful

## Take a screenshot
```
sudo apt-get install scrot
DISPLAY=:0 scrot
```

## Start a youtube video

```
omxplayer `youtube-dl -g https://www.youtube.com/watch?v=bCOc8IS0Uq8` --win "100 80 1380 800"
vlc `youtube-dl -g https://www.youtube.com/watch?v=bCOc8IS0Uq8`
```
