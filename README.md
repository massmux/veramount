# veramount - mounting encrypted veracrypt volume with yubikey

## goal

This procedure and script is for managing an encrypted veracrypt filesystem with a yubikey NFC 5 device. In this way you can mount and dismount the filesystem only with the yubikey connected in which you previously wrote a GPG key. After setting up, just connect the key and launch the script, you will get immediately your filesystem mounted.

 The encrypted filesystem is very important for many purposes: holding bitcoin wallet file, storing very important sensitive data, etc. For example in case you have a bitcoin wallet, it's very important that your wallet file is held into an encrypted filesystem for privacy. It contains infact for example your addresses, your labels, and so on. So it's important even if the wallet is read-only. Always use an encrypted filesystem for storing key informations. Never leave sensitive informations on a filesystem without encryption because your security and privacy is otherwise at risk. 

 With the strategy here described you can achieve the maximum security because the system is based on an external device and not on a password to keep in mind or written somewhere, which is a unsecure way to do.

 What is needed:

 - Ubuntu whatever flavor
 - yubikey 5 NFC device
 - pass
 - GPG2
 - simple mount and dismount script provided

## Install software on Ubuntu

 Install all the software you need

```console
sudo apt-get install gnupg2
sudo apt-get install scdaemon
sudo apt-add-repository ppa:yubico/stable
sudo apt update
sudo apt-get install yubikey-manager yubikey-manager-qt
sudo apt-get install pass
```

## Generate the GPG key

 Generate a new GPG 4096 RSA key to use for credentials encryption

```console
gpg --full-generate-key
gpg --edit-key --expert <your_email@key_identifier>
addkey
#choose (4) RSA (sign only)
#choose 4096
#answer questions
save
```

## backup your keys

 Backup your private key somewhere safe

```console
gpg --export-secret-keys --armor your_email@key_identifier > \<your_email@key_identifier\>.private.gpg-key
gpg --export --armor your_email@key_identifier > \<your_email@key_identifier\>.public.gpg-key
```

## Change the key length on yubikey to 4096

 Prepare your yubikey to accept the RSA 4096 private key you just generated with GPG

```console
gpg --card-edit 
admin
key-attr
#change length to 4096 and exit
```

## move the gpg key to yubikey device

 Move the key to yubikey device

```console
gpg --edit-key AF6FCExxxxxx
gpg> key 1
gpg> keytocard
gpg> <pick the right slot>
gpg> <repeat for the other keys>
gpg> save
```

## activate pass software

 Configure your pass software in order to use the GPG key (inside yubikey) for passwords encryption. This is a key step

```console
pass init "GPG-ID-HERE"
```

 Now you are ready to store keyfile content into pass

 - store your keyfile content and password inside pass in location you prefer
 - configure constants in the script to match pass location you wrote keyfile and password on pass, both on mount and dismount
 - launch the script and test


 NB: It's suggested to set a password and a keyfile content in such a way you have a procedure to recover. For example you can use a words-map like bitcoin BIP39 or some other mechanism like that. Backup should be always on an offline paper.


