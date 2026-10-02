# keyrings

Signing keys for the Omakasui APT repositories, served at
[keyrings.omakasui.org](https://keyrings.omakasui.org) and packaged as `.deb`.

| Key | Repository | Package | Installed as | APT source |
| --- | --- | --- | --- | --- |
| `omakasui-packages.gpg.key` | `packages.omakasui.org` | `omakasui-archive-keyring` | `/usr/share/keyrings/omakasui-packages.gpg` | `/etc/apt/sources.list.d/omakasui.sources` |
| `omakasui-core.gpg.key` | `core.omakasui.org` | `omakasui-core-archive-keyring` | `/usr/share/keyrings/omakasui-core.gpg` | `/etc/apt/sources.list.d/omakasui-core.sources` |

The armored `.key` files in this repository are the source of truth. `debian/rules`
dearmors them and checks each fingerprint against the value pinned in the file, so
the build fails if a key changes unexpectedly.

The `.sources` files are generated from `debian/*.sources.in`. `SUITE` and `PRODUCT`
(`omadeb` on Debian, `omabuntu` on Ubuntu) come from the build environment's
`/etc/os-release`, so each suite's package is built in a container of that distro.
Both can be overridden: `SUITE=noble PRODUCT=omabuntu dpkg-buildpackage -b -uc -us`.

The packages are built and published by:

* `omakasui-archive-keyring`: [build-apt-packages](https://github.com/omakasui/build-apt-packages)
* `omakasui-core-archive-keyring`: [build-apt-omakasui](https://github.com/omakasui/build-apt-omakasui)

Both track this repository's `v*` tags.

## Bootstrap

```bash
CODENAME=$(. /etc/os-release && echo $VERSION_CODENAME)
wget -qO /tmp/omakasui-archive-keyring.deb \
  https://packages.omakasui.org/omakasui-archive-keyring/$CODENAME.deb
sudo dpkg -i /tmp/omakasui-archive-keyring.deb

# core.omakasui.org only (depends on omakasui-archive-keyring):
wget -qO /tmp/omakasui-core-archive-keyring.deb \
  https://core.omakasui.org/omakasui-core-archive-keyring/$CODENAME.deb
sudo dpkg -i /tmp/omakasui-core-archive-keyring.deb

sudo apt-get update
```

