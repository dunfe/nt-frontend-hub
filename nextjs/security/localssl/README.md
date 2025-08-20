# How to Fix Next.js ERR_CERT_AUTHORITY_INVALID in WSL Development

When developing Next.js applications in WSL (Windows Subsystem for Linux), you may encounter SSL certificate validation errors when accessing your development server over HTTPS. This happens because Windows browsers don't trust self-signed certificates by WSL.

To resolve this issue you'll need to establish trust between WSL environment and Windows host system. The following guide will allowing you to develop with HTTPS just like in production.

## 1. Install mkcert in WSL

First, install `mkcert`:

### Linux

On Linux, first install `certutil`.

```
sudo apt install libnss3-tools
    -or-
sudo yum install nss-tools
    -or-
sudo pacman -S nss
    -or-
sudo zypper install mozilla-nss-tools
```

Then you can install using [Homebrew on Linux](https://docs.brew.sh/Homebrew-on-Linux)

```
brew install mkcert
```

or build from source (requires Go 1.13+)

```
git clone https://github.com/FiloSottile/mkcert && cd mkcert
go build -ldflags "-X main.Version=$(git describe --tags)"
```

or use [the pre-built binaries](https://github.com/FiloSottile/mkcert/releases).

```
curl -JLO "https://dl.filippo.io/mkcert/latest?for=linux/amd64"
chmod +x mkcert-v*-linux-amd64
sudo cp mkcert-v*-linux-amd64 /usr/local/bin/mkcert
```

For Arch Linux users, [`mkcert`](https://archlinux.org/packages/extra/x86_64/mkcert/) is available on the official Arch Linux repository.

```bash
# Install mkcert from AUR
yay -S mkcert

# Or install via pacman if available
sudo pacman -S mkcert
```

## 2. Create and Install the Root CA

bash

```bash
# Create local CA
mkcert -install

# This creates a root certificate in ~/.local/share/mkcert/
```

## 3. Generate Certificate for localhost

This is done by NextJS

## 4. Install Root CA in Windows

Since you're using WSL, you need to install the root CA in Windows too:

bash

```bash
# Find the root CA location
mkcert -CAROOT
# Usually outputs: /home/username/.local/share/mkcert

# Copy the rootCA.pem to Windows
cp ~/.local/share/mkcert/rootCA.pem /mnt/c/temp/
```

Then in Windows:

1. Open `certmgr.msc` (Certificate Manager)
2. Navigate to "Trusted Root Certification Authorities" → "Certificates"
3. Right-click → "All Tasks" → "Import"
4. Browse to `C:\temp\rootCA.pem`
5. Complete the import wizard