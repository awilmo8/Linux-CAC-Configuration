# Linux-CAC-Configuration

Instructions on making a CAC/Smart Card work with Linux. I put this together with Gemini, but required some manual tweaks that Gemini missed. I'm hanging it on GitHub so I can find it again later.

## References

- https://militarycac.com/chromebook.htm
- https://wiki.archlinux.org/title/Common_Access_Card
- https://www.cyber.mil/pki-pke/end-users/getting-started/linux

## Ubuntu

```sh
# install relevant packages
sudo apt install opensc pcscd libpcsclite1 coolkey
# once you have opensc installed, plug your CAC in and you'll be able to see the card with this command
pkcs11-tool -L
# enable the pcsc daemon so its always running
sudo systemctl enable --now pcscd

# add the CAC module to the nssdb database used by Chromium browsers (chromium, chrome, edge)
## make sure your CAC is plugged in so the CAC driver module is running
modutil -dbdir sql:$HOME/.pki/nssdb -add "CAC Module" -libfile /usr/lib/x86_64-linux-gnu/opensc-pkcs11.so 

```

### Firefox CAC Configuration

- Plug your CAC in
- Open **Settings**.  
- Search for **"Security Devices"** (usually under Privacy & Security -> Certificates).
- Click **Load**.
- **Module Name:** Give it a name like "CAC Module".
- **Module filename:** Browse to or paste: `/usr/lib/x86_64-linux-gnu/opensc-pkcs11.so`
- Click **OK**. Your certificates should now appear when you visit a protected site.

## DOD Certificates

- Download them from [cyber.mil](https://public.cyber.mil/get-dod-certs/) (CAC Required)
- Manually load them into Firefox and Chrome
- Edge has them built in and works out-of-the-box once you've added the CAC module as outlined above

## Edge

- Download the official Deb binary from [Microsoft's site](https://www.microsoft.com/en-us/edge/download?form=MA13FJ) and install it.
- Once installed it adds the upstream repo source list, so it'll update when you run `apt update`
- I prefer Edge for military sites as its very compatible with DOD website quirks compared to any other browser. Its obvious that Microsoft cares about DOD compatibility.
