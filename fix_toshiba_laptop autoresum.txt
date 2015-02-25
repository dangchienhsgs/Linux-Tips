sudo gedit /etc/pm/sleep.d/20_custom-ehci_hcd

Content: 

#!/bin/bash
 
# /usr/lib/pm-utils/sleep.d/45fix-usb-wakup
# Disable wakup from USB devices on Sony Vaio 1311s9rb
 
function print_state {
    cat /proc/acpi/wakeup | grep $1 | cut -f3 | cut -d' ' -f1 | tr -d '*'
}
 
function disable_wakup {
    dev=$1
    if [ "`print_state $dev`" == "enabled" ]; then
        echo $dev > /proc/acpi/wakeup
    fi
}
 
function enable_wakup {
    dev=$1
    if [ "`print_state $dev`" == "disabled" ]; then
        echo $dev > /proc/acpi/wakeup
    fi
}
 
case $1 in
    hibernate)
            echo "Going to suspend to disk!"
            ;;
    suspend)
            echo "Fixing acpi settings."
 
            # Disable wakup by events from USB hubs
            # (e.g. mouse move on some mices)
            disable_wakup XHC
            disable_wakup EHC1
            disable_wakup EHC2
 
            echo "Suspending to RAM."
            ;;
    thaw)
            echo "Suspend to disk is now over!"
            ;;
    resume)
            echo "We are now resuming."
	    enable_wakup XHC
	    enable_wakup EHC1
            enable_wakup EHC2
            ;;
    *)
            echo "Somebody is callin me totally wrong."
            ;;
esac


Save and close

Continue: sudo chmod 777 -R  /etc/pm/sleep.d/20_custom-ehci_hcd

