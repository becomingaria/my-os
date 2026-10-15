# my-os

My personal KDE Plasma desktop, defined in this repo and built by GitHub every day.
It's built on [Aurora](https://getaurora.dev) (Fedora Kinoite, KDE) using [BlueBuild](https://blue-build.org).

## How updates work

1. A change is pushed to this repo, or the daily build runs at 06:00 UTC.
2. GitHub Actions builds a new signed OS image and publishes it to `ghcr.io/becomingaria/my-os`.
3. The laptop downloads the new image in the background.
4. The new image takes over on the next reboot.
5. If an update breaks something, choose the previous entry in the boot menu.
   To make that rollback permanent, run `rpm-ostree rollback`.

To check for an update right away, run `rpm-ostree upgrade`, then reboot.
To see the current and previous versions, run `rpm-ostree status`.

## Changing the OS

Everything is in [recipes/recipe.yml](recipes/recipe.yml):

- **Desktop apps**: add the Flathub app ID under `default-flatpaks`.
- **System packages**: uncomment the `dnf` module and list Fedora packages.
- **Config files**: put them under `files/system/`, mirroring where they go, e.g. `files/system/etc/...`.

Commit and push. The next build includes your change.

## First-time install

1. Install [Aurora](https://getaurora.dev) (or Fedora Kinoite) on the laptop.
2. Switch to the unsigned image once, so the signing keys get installed:
   ```
   rpm-ostree rebase ostree-unverified-registry:ghcr.io/becomingaria/my-os:latest
   systemctl reboot
   ```
3. Switch to the signed image. From then on, only images signed with this repo's key are accepted:
   ```
   rpm-ostree rebase ostree-image-signed:docker://ghcr.io/becomingaria/my-os:latest
   systemctl reboot
   ```

## Signing

Images are signed with [cosign](https://github.com/sigstore/cosign). `cosign.pub` is the public key.
The private key is stored only in the repo secret `SIGNING_SECRET`. Never commit `cosign.key`.

Verify an image manually:
```
cosign verify --key cosign.pub ghcr.io/becomingaria/my-os
```
