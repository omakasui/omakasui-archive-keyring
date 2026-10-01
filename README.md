# keyrings

Signing keys for the Omakasui APT repositories, served at
[keyrings.omakasui.org](https://keyrings.omakasui.org) and packaged as `.deb`.

| Key | Repository | Package | Installed as |
| --- | --- | --- | --- |
| `omakasui-packages.gpg.key` | `packages.omakasui.org` | `omakasui-archive-keyring` | `/usr/share/keyrings/omakasui-packages.gpg` |
| `omakasui-core.gpg.key` | `core.omakasui.org` | `omakasui-core-archive-keyring` | `/usr/share/keyrings/omakasui-core.gpg` |

The armored `.key` files in this repository are the source of truth. `debian/rules`
dearmors them and checks each fingerprint against the value pinned in the file, so
the build fails if a key changes unexpectedly.

The packages are built and published by:

* `omakasui-archive-keyring`: [build-apt-packages](https://github.com/omakasui/build-apt-packages)
* `omakasui-core-archive-keyring`: [build-apt-omakasui](https://github.com/omakasui/build-apt-omakasui)

Both track this repository's `v*` tags.

## Bootstrap

The first key has to be fetched manually. After that, the package keeps it up to date:

```bash
curl -fsSL https://keyrings.omakasui.org/omakasui-packages.gpg.key \
  | gpg --dearmor | sudo tee /usr/share/keyrings/omakasui-packages.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/omakasui-packages.gpg] \
  https://packages.omakasui.org $(. /etc/os-release && echo $VERSION_CODENAME) main" \
  | sudo tee /etc/apt/sources.list.d/omakasui.list

sudo apt-get update
sudo apt-get install omakasui-archive-keyring
```

The package takes ownership of the file written by `curl`, since the path is the same.
