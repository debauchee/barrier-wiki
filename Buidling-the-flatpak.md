## Install dependencies

Barrier is built against the [KDE runtime and SDK](https://github.com/AdrianKoshka/barrier-manifest/blob/master/com.github.debauchee.barrier.json#L3-L5),
to install them, one should follow these steps:

```shell
$ flatpak --user remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
$ flatpak --user install flathub org.kde.Platform/x86_64/5.12
$ flatpak --user install flathub org.kde.Sdk/x86_64/5.12
```
You should replace `x86_64` with your architecture (`i386`, `arm`, `aarch64`, `x86_64`)
and `5.12` with the current version of the runtimes and SDK used in the [`com.github.debauchee.barrier.json` file](https://github.com/AdrianKoshka/barrier-manifest/blob/master/com.github.debauchee.barrier.json#L3-L5).

## Flatpak builder

`flatpak-builder` can be installed as a flatpak, this provides the benefit of
having a possibly more up-to-date version of `flatpak-builder`. To install,
follow these steps:

```shell
$ flatpak --user install flathub org.flatpak.Builder
```

To make compilation smoother, we use `make`, which can be installed from your package manager.

## Compiling the flatpak

First thing you'll wan to do if you haven't already, is clone the repo:

`git clone https://github.com/flathub/com.github.debauchee.barrier.git`

Then `cd com.github.debauchee.barrier` and `make` or `make FLATPAK_BUILDER="flatpak run org.flatpak.Builder"`.

### Cross compiling

If you're on `x86_64` and want to
compile for `i386`, this can be done via:

`make BUILDER_OPTIONS="--arch=i386"`

Keep in mind though, you'll need to install the `i386` platform and SDK.