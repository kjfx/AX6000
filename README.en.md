# Xiaomi AX6000 SSH Unlock, OpenWrt Flashing, and ShellClash Setup

**Languages:** [简体中文](README.md) | [English](README.en.md) | [Русский](README.ru.md)

This guide shows how to unlock SSH on the Xiaomi AX6000 without using another soft router. The process is quite long, so please watch the video tutorial carefully as you go.

AX6000 SSH unlock video tutorial: https://youtu.be/cLSEmZ6ufE4

## Downloads

Tools package: [Download here](https://github.com/aews/lyq/releases/download/AX6000/AX6000-SSH.zip)

## Step 1. Configure a mobile hotspot

Set the hotspot parameters like this:

- Network name: `OpenWrt`
- Password: `12345678`
- Band: `2.4GHz`

Check the Wi-Fi channel as well, usually channel `1`. You can verify it with another computer or a phone app connected to that hotspot.

## Step 2. Flash the AX6000 firmware and downgrade

Follow the linked video tutorial for the downgrade and flashing part.

## Step 3. Install VMware and deploy OpenWrt

OpenWrt default credentials:

- Address: `192.168.5.1`
- Username: `root`
- Password: `password`

## Step 4. Upload `xqsystem.lua` with WinSCP

Upload `xqsystem.lua` to this directory inside OpenWrt:

```text
/usr/lib/lua/luci/controller/admin/
```

## Step 5. Change the OpenWrt LAN IPv4 address

Set the OpenWrt LAN IPv4 address to:

```text
169.254.31.1
```

## Step 6. Re-enable the mobile hotspot and adjust adapter settings

Turn the hotspot back on, open the hotspot network connection settings, and disable `Internet Protocol Version 4 (IPv4)`.

## Step 7. Edit the VMware bridged network

Open:

```text
Edit > Virtual Network Editor > Change Settings
```

## Step 8. Sign in to the Xiaomi AX6000

Open the following URL after you get the latest `stok` value from the router admin panel:

```text
http://192.168.31.1/cgi-bin/luci/;stok=XXXXXX/api/xqsystem/extendwifi_connect_inited_router?ssid=OpenWrt&password=12345678&encryption=WPA2PSKenctype=CCMP&channel=1&band=2g&admin_username=root&admin_password=admin&admin_nonce=xxx
```

Replace `XXXXXX` and `xxx` with the actual values from your session.

## Step 9. Generate the default root password from the device SN

You can find the SN on the bottom label of the router or in the web UI at `192.168.31.1`.

Password generator:

```text
https://miwifi.dev/ssh
```

If the password generator website is unavailable, use this offline package instead: [Download here](https://github.com/aews/lyq/releases/download/AX6000/SN.zip)

SN format example:

```text
30212/F1ZD22032
```

## Common Issues

Important notes:

1. The `stok` value changes every time you sign in to the Xiaomi router admin panel. Always use the newest one.
2. After SSH is unlocked successfully, there may be no visible confirmation. Use PuTTY to test the login. If you see `ARE U OK`, the unlock worked.
3. If the first attempt fails, sign in to the Xiaomi router admin panel again, copy the latest `stok`, and retry.

### Cannot sign in to OpenWrt at `192.168.5.1`

1. Restart OpenWrt manually or run `reboot`.
2. Confirm whether the OpenWrt IP has been changed to `169.254.31.1`. If it has, use that IP to sign in.
3. Change your local network adapter IP range to match the OpenWrt subnet.
4. In VMware, shut down the running OpenWrt VM, then go to `Edit > Virtual Network Editor > Change Settings > Restore Defaults`.
5. Open the current OpenWrt IP in your browser.

### If you get `{"msg":"一键换机过程中请求对端接口失败","code":1643}`

Inside the virtual machine, run:

```bash
vim /etc/config/dhcp
```

Then:

1. Hold `d` to delete all content.
2. Press `Esc`.
3. Type `:wq`.
4. Press `Enter`.
5. Reboot the VM with `reboot`.

## Install ShellClash

Xiaomi AX6000 ShellClash video tutorial: https://youtu.be/vn5QdyDlTtI

Primary install command:

```bash
export url='https://testingcf.jsdelivr.net/gh/juewuy/ShellCrash@master' && sh -c "$(curl -kfsSl $url/install.sh)" && . /etc/profile &> /dev/null
```

If it does not install, try one of these fallback sources:

```bash
# 1
export url='https://raw.githubusercontent.com/juewuy/ShellCrash/master' && sh -c "$(curl -kfsSl $url/install.sh)" && . /etc/profile &> /dev/null

# 2
export url='https://fastly.jsdelivr.net/gh/juewuy/ShellCrash@master' && sh -c "$(curl -kfsSl $url/install.sh)" && source /etc/profile &> /dev/null

# 3
export url='https://gh.jwsc.eu.org/master' && sh -c "$(curl -kfsSl $url/install.sh)" && . /etc/profile &> /dev/null

# 4
export url='https://testingcf.jsdelivr.net/gh/juewuy/ShellCrash@master' && wget -q --no-check-certificate -O /tmp/install.sh $url/install.sh && sh /tmp/install.sh && . /etc/profile &> /dev/null

# 5
export url='http://t.jwsc.eu.org' && wget -q -O /tmp/install.sh $url/install.sh && sh /tmp/install.sh && . /etc/profile &> /dev/null

# 6
export url='https://testingcf.jsdelivr.net/gh/juewuy/ShellCrash@master' \
&& sh -c "$(curl -kfsSl $url/install.sh)" \
&& . /etc/profile &> /dev/null
```

## Extra Links

- [PuTTY download](https://github.com/aews/lyq/releases/download/AX6000/putty.zip)
- [ShellClash web UI](http://192.168.31.1:9999/ui)
- Preferred provider: http://www.txyun.xyz/
- Telegram: https://t.me/kjfxlyq
