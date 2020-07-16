# The problem...

OpenMandriva Mobile is working, but while we're waiting for our PinePhone,
there is no device in the correct form factor to run it on.
This is because phone handsets, unlike PCs or ARM server hardware, don't
support installing alternative operating systems, and even if a bootloader
can be unlocked, typically only Android drivers are available.

Even devices with open bootloaders and halfway recent kernels (e.g. Sony
Xperia 1) come with a combination that isn't supportable without
considerable work (e.g. Freedreno in kernel 4.14 doesn't support the
variant of Adreno found in the SM8150 chipset).

Pixel 3 is another phone worth looking at, but it uses a display that
requires Display Stream Compression, which is not yet supported by open
drivers.
Others (with a lot more manpower) are facing the same problems even
when trying to run a custom Android build on top of a current
kernel.
https://www.linaro.org/blog/aosp-on-pixel3-pocof1-running-aosp-with-mainline-kernel-on-form-factor-devices/

Most devices not having a serial port or any other way to see kernel
bootup messages doesn't make it easier.

There's libhybris, but that would force us into building a 32-bit OS for
64-bit hardware, so we've opted not to do this.

# The workaround...

Plasma Mobile reference builds have gone the 32-bit/libhybris route and use
the same user interface and a largely comparable system structure (systemd,
simplelogin, plasma-mobile, ...).

So for previews, we're taking the Plasma Mobile reference build, adding 64-bit
libraries and binaries where UI access (and therefore libhybris) isn't
required, and patching some settings.
This includes adding rpm for installation of extra packages.

The result isn't as good as what we're running on devboards, but gives a
good enough preview, with the user visible (without opening a terminal)
differences being mostly the lower speed of a 32-bit OS and the older
versions of some applications in the Plasma Mobile reference build.

# Building the image

Run ./modify-plasma-image. It will:
* Download the TWRP recovery image (necessary for installation)
* Download the Plasma Mobile reference build
* Download relevant OpenMandriva packages and create an OpenMandriva rootfs
* Repackage the root filesystem containing parts of both rootfs images
* Ignore errors about files already existing during decompression, they're
  harmless and caused by conflicts between the PM reference build and OMV
  packages. DNF does the right thing, so there is no need to preprocess
  the rootfs to get rid of those warnings.

# Installing on a Nexus 5X

* Unlock the bootloader as described here: https://forum.xda-developers.com/nexus-5x/general/guides-how-to-guides-beginners-t3206930
* Boot the device into fastboot mode (Power off, then press Volume Down and hold it while turning the phone back on)
* Run: sudo fastboot format cache; sudo fastboot format userdata; sudo ./pm-flash
* Follow the prompts of pm-flash
