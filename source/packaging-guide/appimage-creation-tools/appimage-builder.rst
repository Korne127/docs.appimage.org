.. include:: ../../substitutions.rst

.. _appimage-builder:

appimage-builder
================

`appimage-builder <https://github.com/AppImageCrafters/appimage-builder>`__ is a tool that can be used by both application authors to package their projects as AppImages and other people to turn existing Debian packages into AppImages if none are officially distributed.

It does this by using the system package manager to resolve the application dependencies (instead of relying on them being installed on the host system like most other tools do).

It requires a manual creation of the AppDir folder structure and file placement (if make isn't used). For more information on how to use make accordingly, or manually create the necessary structure, see :ref:`creating-appdir-structure`.

Using appimage-builder, you write a so-called *recipe* that is then used to create the AppImage or convert the package into an AppImage. As some (mostly proprietary) applications don't allow redistribution, you can distribute these recipes to allow other users to easily convert existing packages to AppImages.

appimage-builder creates a complete bundle. This means that it includes all dependencies, even core system libraries like glibc. |increased_appimage_size|. On the other hand, this removes the limitation of requiring an *old system* (meaning the oldest supported LTS distribution version) to compile the binaries, see :ref:`compiling-on-old-system`. It should only be used if linuxdeploy can't be used, e.g. if the AppImage can't be built on the oldest supported LTS distribution version.

The officially supported distributions for AppImages created with appimage-builder are Debian, Ubuntu and Arch.

|appimage_preferred_source|

For more information about appimage-builder and how to use it, please visit: https://appimage-builder.readthedocs.io.
