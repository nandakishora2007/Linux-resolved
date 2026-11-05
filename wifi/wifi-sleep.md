This was among the initial long term problems I faced on a recurring basis. 
My laptop on starting would instantly connect to my wifi. 
But it couldn't reconnect to my wifi(any network for the matter) after it woke from a sleep especially after the laptop's lid was closed.
The problem would be resolved after a reboot.

The script that can help you is(paste it in your terminal):

sudo tee /usr/lib/systemd/system-sleep/iwlwifi-fix.sh <<'EOF'
#!/bin/sh
case "$1" in
  pre)
    rmmod iwlmld 2>/dev/null
    rmmod iwlmvm 2>/dev/null
    rmmod iwlwifi 2>/dev/null
    ;;
  post)
    modprobe iwlwifi
    ;;
esac
EOF
sudo chmod +x /usr/lib/systemd/system-sleep/iwlwifi-fix.sh

This creates an executable file iwlwifi-fix.sh in the directory /usr/lib/systemd/system-sleep/ the code is between the  two EOFs.
pre -> going to sleep  and post -> waking up

During pre this script unloads the 3 wifi modules (iwlmld, iwlmvm, iwlwifi) and during post the module iwlwifi is loaded back.
If you are wondering why we unloaded 3 of the modules but loaded back only one. What is happening is iwlwifi forms the base layer and the other two sits above it and if we try to unload the base without unloading the top layers the stack topples thus causing errors.

Caution : This was tested on Ubuntu 26.04.1 LTS
Wi-Fi card: Intel Wi-Fi 7 BE200 (driver: iwlwifi)
