#ubuntu #linux #xrdp 

- **Alt+F2** and typing `r` works specifically for GNOME Shell (which is what Ubuntu uses)
- From terminal:
    
    ```
    killall -3 gnome-shell
    ```
    
    This is exactly the right command for restarting the GNOME Shell window manager in Ubuntu.
- For display manager restart, Ubuntu uses GDM (GNOME Display Manager) by default:
    
    ```
    sudo systemctl restart gdm3
    ```
    
    Note: Newer Ubuntu versions use gdm3 instead of gdm, but the command I provided will work.
    
**For your RDP session specifically**:

- If RDP disconnects, you can also SSH into the system and restart xrdp:
    
    ```
    sudo systemctl restart xrdp
    ```
    
- Then reconnect via RDP