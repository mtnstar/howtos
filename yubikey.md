# Yubikey

## Desktop Login

### 1. Install requirements

```
sudo add-apt-repository ppa:yubico/stable && sudo apt-get update
sudo apt install libpam-yubico
sudo apt install libpam-u2f
```

### 1. configure pin for yubikey

`ykman fido set-pin`

### 2. Add key

`pamu2fcfg -N > ~/.config/Yubico/u2f_keys`

### 3. Extend pam config

add the following line on top of /etc/pam.d/common-auth

`auth    sufficient      		pam_u2f.so`
