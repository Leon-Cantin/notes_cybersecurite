Si 2 adresses utilisées en même temps sur le même PA, collision sur la couche data-link. Peut causer DOS. 
Peut fonctionner si utilise un PA différent.
Peut fonctionner en utilisant une autre bande. 5GHz/2.4GHz

```shell
sudo macchanger wlan0
sudo ifconfig wlan0 down
sudo macchanger wlan0 -m 3E:48:72:B7:62:2A
sudo ifconfig wlan0 up
```