# fedora-spotifast

Fedora [Copr](https://copr.fedorainfracloud.org/coprs/osrochabr/spotifast/) packaging
for [Spotifast](https://github.com/crmne/spotifast), a native, fast Spotify client.

## Install

```sh
sudo dnf copr enable osrochabr/spotifast
sudo dnf install spotifast
```

Updates arrive through `dnf upgrade`. Spotifast's in-app updater leaves
package-managed installs to the package manager.

## How it works

The package repackages upstream's official Linux release for x86_64 and aarch64.
Before unpacking, `%prep` verifies `checksums.txt` against upstream's Ed25519
update-signing key (`spotifast-update-public-key.pem`, converted from
[`assets/update-public-key.hex`](https://github.com/crmne/spotifast/blob/main/assets/update-public-key.hex))
and the tarball against that manifest, so the build fails for anything upstream
did not sign.

1. [`update.yml`](.github/workflows/update.yml) checks for a new upstream release
   every six hours and commits the new `Version:` once all its assets are published.
2. The push reaches Copr through a GitHub webhook.
3. Copr runs [`.copr/Makefile`](.copr/Makefile) to expand `%autorelease` and
   `%autochangelog` with rpmautospec and build the SRPM, then builds the RPMs.

## Build locally

```sh
sudo dnf install rpm-build rpmdevtools rpmautospec
spectool -g -C ~/rpmbuild/SOURCES spotifast.spec
cp spotifast-update-public-key.pem ~/rpmbuild/SOURCES/
rpmbuild -ba spotifast.spec
```
