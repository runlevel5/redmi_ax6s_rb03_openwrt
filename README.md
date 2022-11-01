## Install OpenWRT on Redmi AX6S (RB03 Chinese version)

Please note this tutorial is based on the [official tutorial](https://openwrt.org/toh/xiaomi/ax3200) but geared toward the RB03 (Chinese version)

Step 1: Connect the router to your computer with the provided CAT5 cable
Step 2: Enable SSH by telneting into it:

Firstly we need to find out the telnet password. Download this [app](https://github.com/YangWang92/AX6S-unlock/raw/master/unlock_pwd.py), then run:

```
unlock_pwd.py <S/N>
```

which would return the telnet password

NOTE: the Serial Number can be found at the back of the router

Secondly telnet into it:

```
telnet 192.168.31.1
username: root
password: the password in the first step
```

Thirdly we enable SSH:

```
nvram set ssh_en=1
nvram set uart_en=1
nvram set boot_wait=on
nvram commit
```

While you are at it, don't forget to udpate the password:


```
passwd root
```

Once updated, please use this newly updated password instead of the generated one.

The SSH daemon might not be up, if so, please enable it:

```
# Start SSHd (dropbear)
/etc/init.d/dropbear enable
/etc/init.d/dropbear start
```

Lastly we can test the SSH connection:

```
ssh root@192.168.31.1
```

Step 3: Flash new firmware:

```
# Download firmware
cd /tmp && curl -L http://downloads.openwrt.org/releases/22.03.2/targets/mediatek/mt7622/openwrt-22.03.2-mediatek-mt7622-xiaomi_redmi-router-ax6s-squashfs-factory.bin

# SCP it to the router
scp openwrt-22.03.2-mediatek-mt7622-xiaomi_redmi-router-ax6s-squashfs-factory.bin root@192.168.31.1:/tmp

# Validate checksum

ssh root@192.168.31.1
cd /tmp
sha256sum openwrt-22.03.2-mediatek-mt7622-xiaomi_redmi-router-ax6s-squashfs-factory.bin
curl -Ls https://downloads.openwrt.org/releases/22.03.2/targets/mediatek/mt7622/sha256sums | grep -i factory | grep -i xiaomi

# Set NVRAM flags
## Run also first commented two lines if after flashing sysupgrade.bin image router restarts to stock firmware instead of OpenWRT
# nvram set flag_boot_rootfs=0
# nvram set "boot_fw1=run boot_rd_img;bootm"
nvram set flag_boot_success=1
nvram set flag_try_sys1_failed=0
nvram set flag_try_sys2_failed=0
nvram commit

# Flash image
mtd -r write openwrt-22.03.2-mediatek-mt7622-xiaomi_redmi-router-ax6s-squashfs-factory.bin firmware
```

Once done, the router should reboot automatically. The new IP for the web GUI is 192.168.1.1.
You can login with browser (default password is blank), don't forget to set password
