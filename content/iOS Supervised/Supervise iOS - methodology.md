# requirements
- an iOS device
- be sure to remove the find my device on the device before proceeding 
- a mac with Apple Configurator

https://support.apple.com/guide/apple-configurator-mac/supervise-devices-apd9e4f64088/mac

https://support.addigy.com/hc/en-us/articles/4414567796755-Overview-Apple-Configurator
https://support.apple.com/en-gw/guide/apple-configurator-mac/cad99bc2a859/mac
https://support.apple.com/en-gw/guide/apple-configurator-mac/cad99bc2a859/mac
https://www.youtube.com/@alientechchampion

## Disable "Find my device"
[[RPReplay_Final1757406563.MP4]]

Screenshots of the process, go to the main page of Settings and follow along
|     |     |
| --- | --- |
| Screen 1| Screen 2|
|  ![[iOS_Disable_FindMyPhone1.PNG]]   |![[iOS_Disable_FindMyPhone2.PNG]]     |
| Screen 3 | Screen 4|
| ![[iOS_Disable_FindMyPhone3.PNG]] | ![[iOS_Disable_FindMyPhone4.PNG]]|
| Screen 5 | ... |
| ![[iOS_Disable_FindMyPhone5.PNG]]||


# put an iOS in a supervised state
You have 2 officials ways to do so, command line or GUI :
  - cmdline : cfgutil
	  - you can find cfgutil once Apple Configurator install, most commonly here : `/Applications/Apple Configurator.app/Contents/MacOS/cfgutil`
  - GUI: Apple Configurator for Mac (Yes +10years ago one existed for Windows)

|     |     |
| --- | --- |
|Apple Configurator| cfgutil    |
| ![[Supervise iOS - AppleConfig NewProfile.png]]    | ![[Supervise iOS - cfgutil help.png]]    |


## Supervise with cfgutil

First look for:

```
$ cfgutil list
Type: iPhone14,4 ECID: 0x1C604A3405801E UDID: 00008110-001C604A3405801E Location: 0x120000 Name: iPhone
```

### You need to pair the device (won't failed if already paired)
```
$ cfgutil pair
Waiting for the device [1/1] [*******************************************]  100%
Paired with iPhone (ECID: 0x1C604A3405801E).

--- Summary ---
Operation "pair" succeeded on 1 devices
```

### Backup
```
$ cfgutil backup

  

Waiting for the device [1/2] [*******************************************]  100%

Step 2 of 2: Backing up [2/2] [******************************************]  100%

Backed up iPhone (ECID: 0x1C604A3405801E).

  

--- Summary ---

Operation "backup" succeeded on 1 devices.
```

### List backups
```
$ cfgutil list-backups
Source: 00008110-001C604A3405801E Encrypted: No Date: Today at 12:42 Name: iPhone
```

### Erase device
a synequanone condition to prepare a device is for it to be erased
see troubleshooting : prepare fails
the below output is written, then few second after you'll see your device presenting you with apple Logo and a progress bar

```
$ cfgutil erase
Waiting for the device [1/1] [*******************************************]  100%
Erased device iPhone (ECID: 0x1C604A3405801E).

--- Summary ---
Operation "erase" succeeded on 1 devices.

We will trap traffic between apple configurator and internet basically :)
mitm is used and launched like this : mitmproxy --mode regular --showhost
```

```
enola@Next:~$ cfgutil list
Type: iPhone14,4	ECID: 0x1C604A3405801E	UDID: 00008110-001C604A3405801E Location: 0x120000 Name: iPhone
enola@Next:~$ cfgutil pair

Waiting for the device [1/1] [*******************************************]  100%
Paired with iPhone (ECID: 0x1C604A3405801E).

--- Summary ---
Operation "pair" succeeded on 1 devices.
enola@Next:~$ cfgutil prepare

Waiting for the device [1/3] [*******************************************]  100%
Step 2 of 3: Downloading activation record for device [2/3] [************]  100%
Step 3 of 3: Activating iOS on the device [3/3] [************************]  100%
"prepare" succeeded on iPhone (ECID: 0x1C604A3405801E).

--- Summary ---
Operation "prepare" succeeded on 1 devices.q
```

### export supervising cert
I assume you already have prepared at least a device with Apple Configurator GUI, thus have been presented with the option to create an "organization"
export the certificate and it's private key from Apple KeyChain

I did it for each
Right Click, on the cer 
Click on export
![[Supervise iOS - KeychainExport1.png]]
repeat for the privatekey for which you'll be prompted for a password
![[Supervise iOS - methodology.png]]

once you have exported them, you'll have 2 file as below :
![[Supervise iOS - KeyChain exportedSupervisingIdentity.png]]

to see what's in it :
#### see private key content with openssl 
we will read the p12 with openssl
command to read p12 file `openssl pkcs12 -in CERTIFICATE_ABSOLUTEPATH -info -nodes -passin pass:YOURPASS`
```bash
openssl pkcs12 -in ~/Workspace/AppleConfig\ -\ cfgutil/AppleConf_SupervisionIdentity_PrivateKey_awesome\ \(6CF786FC-78AC-4922-B56C-2D44835863D8\).p12 -info -nodes -passin pass:awesome
MAC: sha1, Iteration 1
MAC length: 20, salt length: 8
PKCS7 Data
Shrouded Keybag: pbeWithSHA1And3-KeyTripleDES-CBC, Iteration 2048
Bag Attributes
    friendlyName: Apple Configurator: awesome (6CF786FC-78AC-4922-B56C-2D44835863D8)
    localKeyID: 29 95 0B 37 49 44 BE 19 A1 6C 7C 97 EC 2A DF E5 C8 DF 87 97 
Key Attributes: <No Attributes>
-----BEGIN PRIVATE KEY-----
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDRhMEp/uGethwb
cVKK5FNzEBdhtNlVCktFSLA7tEC9UrkyfK3YPgWOMeCd4VqpeBFYvI3v03xqqwdJ
W252fOMxyJ9wDYw4BbYqEfBw87zcJ5Rrcsly/sU3iEdXQhn3oQJFEklc5r3WvT3V
27HpDUT02gZIsVKnIla2EuknTe2EwM24Hbm3HVVmWTiBmz2dbz1gxfXAMob3W8JA
venX2IV8MeSvVvKN0rBYIg1bau5w/P7/LJ0tYFj1Ped4tl5rgdoFf9ACvIaZ9Xe1
iRvXWQNvHG6C/WEh67HXqnINR/T3rdt5u2ZMLeQcLTnQa9y2Q1NcEBRBj8FjOxaw
brjqCyXtAgMBAAECggEBAI/y+NAEqTjk/8yvADojA16jqJzdpxAxYWO5vDNY9b3d
rxYL6VkPy7tVc3ClmyeiMbDY41/p2qpi1T/GTM+loGbc4wYWmMcIzY58Asln/NL5
cpScKeITPqaXwAQoizTCb4/LL5JfigCWxnw/VC29iyn6/aRGCHaCNjckKQJzHQQ+
QRuFujI9w3Gf6FMgNstelvfBc3v9VnD5yCwOJuRICMFWVEgBw/8MqNeS9eVuGoMQ
FMc8lZZw6nfk8Tnod4GQsTShYfo0oSJIGKmbWXuYAplKwrmpTB+oxHR/d1oU61CW
hY4V3JFLmIMV7aNK6kfXSObWVoKzbRflQ9VZxL28Y6ECgYEA6AdpX6JQmuv3SHwW
5qJb2HMl9Q152jsN/9W2M+L+wkBWKZ5Kn/dApUjFJGp03c4As2YUCI/pIN2I8hWL
4+0dDBNlOoZGj/0OymYck2V3ILEAvJgKbe3cXLCydY9YOPhiH4gAUAM5WKqWIGiE
iD/o3Fwta9n+SRhBKjXNQztsadkCgYEA5yn/2db8yKwoLGU8DhhmNnKOBh0nj9Wh
m6fJ+mg5+Nn4kdPyL7tHIz0wYmAJ0oWZf+n7MvA+TtjSuh37KV4SGWD5FlsFY5Hk
hzgf8vfhbgYSqpY33Wq3Cs09rgMeAwu4M21DMwkZySIJWblz9m/xUDrGMcENZb1E
9lQV3oxBnDUCgYAXqvHftHHea6VsumOnoPYXbR95EKfWT+HMr+MHBeeQrvlbA29/
Q7xPX83kOguzuFiv9AClIvDXzmEyuGntlPk6ixvvTVUTSO/iS2osytPM/OEjW6rs
ra+lsMxzW2zXWta/eqL1hm6qEbSAl8i1ETfSioCDmNfsYtH62UQX0I7teQKBgGlO
MbZsAKXt/zMSPwRwywdcsiRI3b/hYwiErDc9icM6kMjl03s5BlQgSM1X0MGtiNrD
nWJ8HPZQQdb1V3hl2TrkeTRc7JyKVp/eynclwvUbIR/C5NoiBhaOnt2Jn/9lNFmB
Gc7DA5MjxTyxhgkqv7R7wdPijRbe3O6WKYxDOpRNAoGAZDjidL23alXAknmqlouS
oH1FgZ8iYJTU6qeD4ffo2Zn0GHlix1cgFsXHHNSkQtht/Uqo2oOu1xdqaVvYqAmp
11J0I+iSrV/v20m5a7pwjWdS8puySckTRtqgS+fQI7K+KaOIJc2fhlN9LcDuM16u
qrXXKyZWoAB6k4Ef01vfSiw=
-----END PRIVATE KEY-----
```

let's retrieve the file format :
`file supervisionIdentity_key.der supervisionIdentity_key.der: DER Encoded Key Pair, 2048 bits`



#### extract the private key from p12
we will put the private key in a PEM file,
then we'll convert it to DER
form of the command used : ```bash
cat > ABSOLUTE_FILENAME <<'EOF' 
-----BEGIN PRIVATE KEY-----
-----END PRIVATE KEY-----
EOF```
```bash
cat > supervisionIdentitity_key.pem <<'EOF'
-----BEGIN PRIVATE KEY-----
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDRhMEp/uGethwb
cVKK5FNzEBdhtNlVCktFSLA7tEC9UrkyfK3YPgWOMeCd4VqpeBFYvI3v03xqqwdJ
W252fOMxyJ9wDYw4BbYqEfBw87zcJ5Rrcsly/sU3iEdXQhn3oQJFEklc5r3WvT3V
27HpDUT02gZIsVKnIla2EuknTe2EwM24Hbm3HVVmWTiBmz2dbz1gxfXAMob3W8JA
venX2IV8MeSvVvKN0rBYIg1bau5w/P7/LJ0tYFj1Ped4tl5rgdoFf9ACvIaZ9Xe1
iRvXWQNvHG6C/WEh67HXqnINR/T3rdt5u2ZMLeQcLTnQa9y2Q1NcEBRBj8FjOxaw
brjqCyXtAgMBAAECggEBAI/y+NAEqTjk/8yvADojA16jqJzdpxAxYWO5vDNY9b3d
rxYL6VkPy7tVc3ClmyeiMbDY41/p2qpi1T/GTM+loGbc4wYWmMcIzY58Asln/NL5
cpScKeITPqaXwAQoizTCb4/LL5JfigCWxnw/VC29iyn6/aRGCHaCNjckKQJzHQQ+
QRuFujI9w3Gf6FMgNstelvfBc3v9VnD5yCwOJuRICMFWVEgBw/8MqNeS9eVuGoMQ
FMc8lZZw6nfk8Tnod4GQsTShYfo0oSJIGKmbWXuYAplKwrmpTB+oxHR/d1oU61CW
hY4V3JFLmIMV7aNK6kfXSObWVoKzbRflQ9VZxL28Y6ECgYEA6AdpX6JQmuv3SHwW
5qJb2HMl9Q152jsN/9W2M+L+wkBWKZ5Kn/dApUjFJGp03c4As2YUCI/pIN2I8hWL
4+0dDBNlOoZGj/0OymYck2V3ILEAvJgKbe3cXLCydY9YOPhiH4gAUAM5WKqWIGiE
iD/o3Fwta9n+SRhBKjXNQztsadkCgYEA5yn/2db8yKwoLGU8DhhmNnKOBh0nj9Wh
m6fJ+mg5+Nn4kdPyL7tHIz0wYmAJ0oWZf+n7MvA+TtjSuh37KV4SGWD5FlsFY5Hk
hzgf8vfhbgYSqpY33Wq3Cs09rgMeAwu4M21DMwkZySIJWblz9m/xUDrGMcENZb1E
9lQV3oxBnDUCgYAXqvHftHHea6VsumOnoPYXbR95EKfWT+HMr+MHBeeQrvlbA29/
Q7xPX83kOguzuFiv9AClIvDXzmEyuGntlPk6ixvvTVUTSO/iS2osytPM/OEjW6rs
ra+lsMxzW2zXWta/eqL1hm6qEbSAl8i1ETfSioCDmNfsYtH62UQX0I7teQKBgGlO
MbZsAKXt/zMSPwRwywdcsiRI3b/hYwiErDc9icM6kMjl03s5BlQgSM1X0MGtiNrD
nWJ8HPZQQdb1V3hl2TrkeTRc7JyKVp/eynclwvUbIR/C5NoiBhaOnt2Jn/9lNFmB
Gc7DA5MjxTyxhgkqv7R7wdPijRbe3O6WKYxDOpRNAoGAZDjidL23alXAknmqlouS
oH1FgZ8iYJTU6qeD4ffo2Zn0GHlix1cgFsXHHNSkQtht/Uqo2oOu1xdqaVvYqAmp
11J0I+iSrV/v20m5a7pwjWdS8puySckTRtqgS+fQI7K+KaOIJc2fhlN9LcDuM16u
qrXXKyZWoAB6k4Ef01vfSiw=
-----END PRIVATE KEY-----
EOF
```
now convert with the a command taking this form :
`openssl pkey -in supervisionIdentity_key.pem -out supervision_key.der -outform DER`

verify format of created file:
command of form `file YOUR_FILEPATH`
```bash
file supervisionIdentity_key.der 

supervisionIdentity_key.der: DER Encoded Key Pair, 2048 bits
```



#### see supervising identity certificate content
```bash
openssl x509 -in ~/Workspace/AppleConfig\ -\ cfgutil/AppleConf_SupervisionIdentityCER_awesome\ \(6CF786FC-78AC-4922-B56C-2D44835863D8\).pem -text -noout

Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 546831495 (0x2097fc87)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN=Apple Configurator: awesome (6CF786FC-78AC-4922-B56C-2D44835863D8), O=awesome, C=FR
        Validity
            Not Before: Sep  9 08:53:02 2025 GMT
            Not After : Sep  4 08:53:02 2045 GMT
        Subject: CN=Apple Configurator: awesome (6CF786FC-78AC-4922-B56C-2D44835863D8), O=awesome, C=FR
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:d1:84:c1:29:fe:e1:9e:b6:1c:1b:71:52:8a:e4:
                    53:73:10:17:61:b4:d9:55:0a:4b:45:48:b0:3b:b4:
                    40:bd:52:b9:32:7c:ad:d8:3e:05:8e:31:e0:9d:e1:
                    5a:a9:78:11:58:bc:8d:ef:d3:7c:6a:ab:07:49:5b:
                    6e:76:7c:e3:31:c8:9f:70:0d:8c:38:05:b6:2a:11:
                    f0:70:f3:bc:dc:27:94:6b:72:c9:72:fe:c5:37:88:
                    47:57:42:19:f7:a1:02:45:12:49:5c:e6:bd:d6:bd:
                    3d:d5:db:b1:e9:0d:44:f4:da:06:48:b1:52:a7:22:
                    56:b6:12:e9:27:4d:ed:84:c0:cd:b8:1d:b9:b7:1d:
                    55:66:59:38:81:9b:3d:9d:6f:3d:60:c5:f5:c0:32:
                    86:f7:5b:c2:40:bd:e9:d7:d8:85:7c:31:e4:af:56:
                    f2:8d:d2:b0:58:22:0d:5b:6a:ee:70:fc:fe:ff:2c:
                    9d:2d:60:58:f5:3d:e7:78:b6:5e:6b:81:da:05:7f:
                    d0:02:bc:86:99:f5:77:b5:89:1b:d7:59:03:6f:1c:
                    6e:82:fd:61:21:eb:b1:d7:aa:72:0d:47:f4:f7:ad:
                    db:79:bb:66:4c:2d:e4:1c:2d:39:d0:6b:dc:b6:43:
                    53:5c:10:14:41:8f:c1:63:3b:16:b0:6e:b8:ea:0b:
                    25:ed
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: critical
                Code Signing
            X509v3 Subject Key Identifier: 
                29:95:0B:37:49:44:BE:19:A1:6C:7C:97:EC:2A:DF:E5:C8:DF:87:97
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
        c1:71:fb:47:1e:47:a7:0e:0e:7f:80:26:e2:58:10:ba:1f:5f:
        cc:45:61:72:b8:8f:43:ea:cd:b2:b9:f5:e5:96:46:de:88:73:
        89:b0:f7:0f:93:73:bc:86:9e:ab:7d:9a:37:d1:c3:7b:08:8d:
        d4:ed:5e:0c:7a:aa:06:d7:96:d6:cb:04:f4:dc:ac:1d:9c:7d:
        11:07:1e:a1:61:b0:0d:40:94:a5:68:54:3d:1e:a6:47:da:a0:
        bd:19:46:b8:37:b3:df:ee:3d:70:68:11:d1:55:a7:da:73:1a:
        ad:9b:70:2f:db:8b:d8:fe:1c:8f:5f:3c:06:a8:d9:8b:77:dc:
        f8:fd:02:25:c8:32:2c:62:2e:6a:20:e1:a7:8c:37:ac:90:24:
        a6:62:b6:20:39:ed:2b:d3:f7:ef:8b:9a:12:6f:84:69:8f:b6:
        cb:73:6b:88:50:f5:ba:26:bc:dc:00:80:8b:59:ad:40:f7:5c:
        a4:96:bc:8f:ce:4e:4c:84:17:e9:6b:10:12:cd:8f:db:7a:5c:
        6f:6b:87:3b:ce:e7:d5:41:46:69:b6:d5:f2:e9:b8:49:d2:83:
        6d:65:4e:ed:ba:9a:19:34:54:eb:01:80:99:49:fe:be:0d:6f:
        20:a2:6a:98:9e:62:93:65:c3:43:7e:ea:91:32:54:fc:83:28:
        76:79:b7:d0
```

verify format of the file
```bash
file AppleConf_SupervisionIdentityCER_awesome\ \(6CF786FC-78AC-4922-B56C-2D44835863D8\).pem

AppleConf_SupervisionIdentityCER_awesome (6CF786FC-78AC-4922-B56C-2D44835863D8).pem Certificate, Version=3
```

which indicate PEM format, cfgutil expects a DER one


### Supervising with Apple Configurator 
now we shall have everything right
 certicate, with it's key encoded in DER binary
Command will be of form : 
```
$ cfgutil --certificate supervision_cert.der --private-key supervisionIdentity_key.der --ecid 0x1C604A3405801E --verbose prepare
Waiting for the device [1/3] [*******************************************]  100%
Step 2 of 3: Downloading activation record for device [2/3] [************]  100%
Step 3 of 3: Activating iOS on the device [3/3] [************************]  100%
"prepare" succeeded on iPhone (ECID: 0x1C604A3405801E).

--- Summary ---
Operation "prepare" succeeded on 1 devices.
```
```bash
cfgutil --certificate supervision_cert.der --private-key supervisionIdentity_key.der --ecid 0x1C604A3405801E --verbose --progress prepare --supervised --host-cert supervision_cert.der --name awesome --language en --locale en_US --skip-accessibility-appearance --skip-action-button --skip-all --skip-android --skip-app-store --skip-appearance --skip-apple-intelligence --skip-appleid --skip-applepay --skip-camera-controls --skip-diagnostics --skip-dictation-setup --skip-feature-highlights --skip-home-button --skip-imessage-and-facetime --skip-intended-user --skip-keyboard-setup --skip-language --skip-location --skip-multitasking --skip-passcode --skip-phone-number-permission --skip-preferred-language-setup --skip-privacy --skip-region --skip-restore --skip-restore-completed --skip-safety --skip-screen-saver --skip-screentime --skip-sim-setup --skip-siri --skip-software-update --skip-tap-to-setup --skip-terms-of-address --skip-touchid --skip-true-tone --skip-tv-home-screen-sync --skip-tv-provider --skip-update-completed --skip-watch-migration --skip-web-content-filtering --skip-welcome --skip-where-is-this-apple-tv --skip-zoom
Waiting for the device [1/3] [*******************************************]  100%
Step 2 of 3: Downloading activation record for device [2/3] [************]  100%
Step 3 of 3: Activating iOS on the device [3/3] [************************]  100%
"prepare" succeeded on iPhone (ECID: 0x1C604A3405801E).
```

our device is now supervised

### verify your device isSupervised
The command below shall return isSupervised
```bash
$ cfgutil get isSupervised
yes
```



## Restore a backup

⚠️ tldr; In practice it's not supported by apple to restore a unsupervise backup to a supervised device.
OFFICIALY :
Restoring a backup to a device will overwrite its current configuration, including its supervision status 

```
Restore from a backup

    Connect one or more devices to a Thunderbolt or USB port on the Mac that has Apple Configurator  installed, then select them in the device window.

    Do one of the following:

        Choose Actions > Restore from Back Up.

        Control-click the selected devices, and choose Restore from Back Up.

    Select the backup you want to restore, then click Restore.

    All contents on the devices will be erased. If the devices are supervised, they will be unsupervised after the restore.
    
```
cf: https://support.apple.com/en-sg/guide/apple-configurator-mac/cadbf9b61c/mac

## Restoring backup of unsupervised device to supervised one with Apple Config

as you understood the below is NOT SUPPORTED BY APPLE and provided as is

### Restoring backup of unsupervised device to supervised one with Apple Config
Aim:  restore the backup of a unsupervised device to a supervised one

one thing I didn't tried :
backup in unsupervised state
backup in supervised state
 -> make a diff (on binary format), there might be a flag in there...)

What I observed, and many other is that by design since Apple Configurator 2 at least, when you restore a backup it does restores it with the field isSupervised to false, then activate the device
 So when you look 
   A. your device is restored
   B. your device isn't supervised anymore
   
I was left with, I have a device shall get somehow the flag isSupervised set to true just before activating it.
and by chance...

=> https://apple.stackexchange.com/questions/285128/how-to-supervise-an-unsupervised-ios-device-keeping-the-users-data
So here is what we seeked among all, a way to keep/restore this supervised state before activation but after/during restoration of a device
I tried it several time didn't automate it, and I won't have time to do so before a while.
The below assumes when you do `cfgutil backup` it will store it in `~/Library/Application\ Support/MobileSync/Backup/$DEVICE_UDID`
Since we can't override the destination (from what I found), will let it do is thing,archive backup folder, copy the whole content of the folder to $DEVICE_UDID.unsupervised.bkp,
then erase, supervise,backup, 

0. Deactivate "find my iPhone"
1. Create a non-encrypted backup of the iOS device on a Mac.
`cfgutil backup`
2. Make a zip out of the freshly created  ~/Library/Application Support/MobileSync/Backup/xyz from being overwritten by a newer backup.
`zip ~/Library/Application\ Support/MobileSync/Backup/xyz /tmp`
mv ~/Library/Application\ Support/MobileSync/Backup/`cfgutil get UDID` ~/Library/Application\ Support/MobileSync/Backup/`cfgutil get UDID`.unsupervised
3. erase and Supervise the device with cfgutil has explained above
4. Create another non-encrypted backup of the device that was just set up. Be sure to archive this backup and make a backup copy of it as well.
`cfgutil backup`

5. Use Terminal to locate the file containing supervision data in both backups:
    - Navigate to `~/Library/Application Support/MobileSync/Backup` and `cd` into the folder of one of the backups.
    - Type `sqlite3 Manifest.db` and hit Enter.
    - Query the database by typing `select fileID from files where relativePath = 'Library/ConfigurationProfiles/CloudConfigurationDetails.plist';` and hitting Enter.
    ![[Supervise iOS - methodology XCode change supervision flag.png]]
    - Note down the string of text that the query outputs is the name of the file containing the supervision data.
    - Do the same for the other backup. It should return the same name both times.
6. Locate the file with that name in both backups. It is usually in the `1e` folder.
7. Replace the file in the first (unsupervised) backup with the file from the second (supervised) backup. This (first) backup now becomes supervised.
8. Restore this modified (first) backup to the same device. The device is now supervised and all the data from the old (unsupervised) backup restored.

They are quite different,plist differs quite a lot...
when I compare both file between supervised and unsupervised :
```bash

$ cmp -l 00008110-001C604A3405801E/1e/1e6c0783f9b33d00b152067a0661c8fc8841073f 00008110-001C604A3405801E.bkp/1e/1e6c0783f9b33d00b152067a0661c8fc8841073f 
     9 331 325
    18  11   7
    19  12   6
    20  14 134
    21  14 111
    22  16 163
    23  14 123
    24  20 165
    25  14 160
    26  14 145
    27  23 162
    28 137 166
    29  20 151
    30  32 163
    31 123 145
    32 165 144
    33 160 137
    34 145  20
    35 162  34
    36 166 103
    37 151 154
    38 163 157
    39 157 165
    40 162 144
    41 110 103
    43 163 156
    44 164 146
    45 103 151
    46 145 147
    47 162 165
    48 164 162
    49 151 141
    50 146 164
    52 143 157
    53 141 156
    54 164 125
    55 145 111
    56 163 103
    57 133 157
    58 111 155
    59 163 160
    60 115 154
    61 141 145
    62 156 164
    63 144 145
    64 141 137
    65 164  20
    66 157  23
    67 162 103
    68 171 157
    69 134 156
    70 101 146
    71 154 151
    72 154 147
    73 157 165
    74 167 162
    75 120 141
    76 141 164
    78 162 157
    79 151 156
    80 156 123
    81 147 157
    82 137 165
    83  20 162
    84  20 143
    85 117 145
    86 162 134
    87 147 101
    88 141 154
    89 156 154
    90 151 157
    91 172 167
    92 141 120
    93 164 141
    95 157 162
    96 156 151
    97 116 156
    98 141 147
    99 155 137
   100 145  20
   101 134  34
   102 111 120
   103 163 157
   104 123 163
   105 165 164
   106 160 123
   108 162 164
   109 166 165
   110 151 160
   111 163 120
   112 145 162
   113 144 157
   114 137 146
   115  20 151
   116  20 154
   117 111 145
   118 163 127
   119 115 141
   120 104 163
   121 115 111
   122 125 156
   123 156 163
   124 162 164
   125 145 141
   126 155 154
   127 157 154
   128 166 145
   129 141 144
   130 142  10
   131 154  11
   132 145  20
   133 137   0
   134  20  11
   135  34  10
   136 103  10
   137 154  23
   138 157  40
   139 165  77
   140 144 125
   141 103 142
   142 157 201
   143 156 202
   144 146 203
   145 151 205
   146 147 206
   147 165   0
   148 162   0
   149 141   0
   150 164   0
   151 151   0
   152 157   0
   153 156   1
   154 125   1
   155 111   0
   156 103   0
   157 157   0
   158 155   0
   159 160   0
   160 154   0
   161 145   0
   162 164  13
   163 145   0
   164 137   0
   165  20   0
   166  27   0
   167 103   0
   168 157   0
   169 156   0
   170 146   0
   171 151   0
   172 147   0
   173 165   0
   174 162   0
   175 141   0
   176 164   0
   177 151   0
   178 157 207
```

Be sure to make a backup of the backup before modifying to prevent data loss!



cfgutil --certificate supervision_cert.der --private-key supervisionIdentity_key.der --ecid 0x1C604A3405801E --verbose restore-backup  --source 00008110-001C604A3405801E

### Whole workflow
so after multiple tests
you want to execute commands in a row

## Troubleshotting
```
$ cfgutil prepare

Waiting for the device [1/1] [*******************************************]  100%
cfgutil: error: The device is already prepared and must be erased to change settings.
(Domain: ConfigurationUtilityKit.error Code: 612)

"prepare" failed on iPhone (ECID: 0x1C604A3405801E).

--- Summary ---
Operation "prepare" failed on 1 devices.
```


### restoring

#### cfgutil: error: The required framework “MobileDevice” is out of date. 
when restoring I had the following which is self explanatory, so I updated to the latest macOSx version

```
$ cfgutil --certificate supervision_cert.der --private-key supervisionIdentity_key.der --ecid 0x1C604A3405801E --verbose restore iPhone

Waiting for the device [1/4] [*******************************************]  100%
Step 2 of 4: Downloading iOS [2/4] [*************************************]  100%
Step 3 of 4: Unzipping iOS [3/4] [***************************************]  100%
cfgutil: error: The required framework “MobileDevice” is out of date. Please update macOS.
(Domain: ConfigurationUtilityKit.error Code: 401)

"restore" failed on iPhone (ECID: 0x1C604A3405801E).

--- Summary ---
Operation "restore" failed on 1 devices.
```

![[Supervise iOS - cfgutile Restore Error.png]]

#### Everything is ok but my backup isn't restored
DO NOT USE restore !!!
use restore-backup
```bash
cfgutil --certificate supervision_cert.der --private-key supervisionIdentity_key.der --ecid 0x1C604A3405801E --verbose restore "Omer's iPhone"

Waiting for the device [1/4] [*******************************************]  100%
Step 2 of 4: Downloading iOS [2/4] [*************************************]  100%
Step 3 of 4: Unzipping iOS [3/4] [***************************************]  100%
Step 4 of 4: Installing iOS [4/4] [**************************************]  100%
Restored System on iPhone (ECID: 0x1C604A3405801E).

--- Summary ---
Operation "restore" succeeded on 1 devices.
```

![[Supervise iOS - AC2 prepare error.png]]