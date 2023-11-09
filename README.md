# luks-tpm-helper
Interactive helper to enable automatic LUKS disk decryption using the TPM2

## Features
Find all LUKS2 encrypted partitions on the host and, for each one, prompt the user to automatically unlock it using the TPM.

## Usage
Just clone and run it. It's interactive.

``` sh
git clone https://github.com/Arctize/luks-tpm-helper.git
cd luks-tpm-helper
./luks-tpm-helper
```

## Requirements

The script uses either `systemd-cryptenroll` or `clevis` to do the heavy lifting. The former should be present out of the box on most recent distributions with systemd 248 or higher.

### Fedora
For unlocking encrypted root, make sure the initramfs contains the tpm2 modules:
``` sh
echo 'add_dracutmodules+=" tpm2-tss "' | sudo tee /etc/dracut.conf.d/tpm2.conf && sudo dracut -f
```

### Arch Linux
Add the `systemd` and `sd-encrypt` modules to `/etc/mkinitcpio.conf`, for example:
```
HOOKS=(base systemd autodetect modconf kms keyboard sd-vconsole block filesystems fsck sd-encrypt)
```
and then rebuild the initramfs
```
sudo mkinitcpio -p linux
```
Check the [Wiki entry](https://wiki.archlinux.org/title/Mkinitcpio#HOOKS) for more info.

### Without systemd (or old systemd, like Ubuntu 20.04)

Install `clevis` before running the script:

``` sh
sudo apt install clevis-luks clevis-tpm2
```

## Note
- Secure boot should be enabled when running the script. It will warn you if it's not. The secret token is sealed only against PCR7. This means the TPM will release the token as long as the Secure Boot configuration on the device doesn't change. You can [seal against more registers](https://www.freedesktop.org/software/systemd/man/latest/systemd-cryptenroll.html).
- A further recommendation is to enroll your own Secure Boot keys and use a signed [Unified Kernel Image](https://wiki.archlinux.org/title/Unified_kernel_image). A helpful tool for that is https://github.com/Foxboron/sbctl.
