.. TODO: create extra page on AppDir concept and move label there

.. include:: ../substitutions.rst

.. _ref-appdir-specification:
.. _ref-appdir:

AppDir specification
====================

This page describes the *AppDir* format. AppDirs are the "source" of AppImages. When building an AppImage, a file system image is built from such a directory, to which a runtime is prepended.


.. contents:: Contents
   :local:
   :depth: 1


History
-------

The AppDir format has first been described by `ROX Filer`_, and has since been extended and modified by the AppImage project to suit their needs.

.. _ROX Filer: http://rox.sourceforge.net/desktop/AppDirs.html


Required content
----------------

As the name intends, AppDirs are normal directories with some special content. An AppImage |must| contain the following four types of entries in its root to conform to this specification:


.. _apprun-specification:

AppRun
++++++

Every AppImage's AppDir must contain a file (executable, script, etc.) or symlink called :code:`AppRun`, providing the "entry point" of the application. When running the AppImage, the :ref:`runtime <ref-runtime>` executes the :code:`AppRun` file within the :ref:`AppDir <ref-appdir>`.

It is located in the root directory that makes up an AppDir, so it can be used to calculate paths relative to the (later mounted) AppDir.

In modern AppImages (especially if modern :ref:`appimage-creation-tools` are used), :code:`AppRun` is usually a symlink to the main binary. This works if the binary has been made :ref:`relocatable <removing-hard-coded-paths>`, which modern AppImage creation tools do.

However, if an existing application must not be altered, e.g. if the licence prohibits any modifications, AppImageKit's `AppRun.c binary <https://github.com/AppImage/AppImageKit/releases/continuous>`_ can be used. :code:`AppRun.c` attempts to make programs use bundled shared libraries instead of system ones by manipulating environment variables. Furthermore, it attempts to prevent warnings users might encounter, that are coming from the fact the :ref:`AppDir <ref-appdir>` is mounted read-only. But using :code:`AppRun.c` is not a guarantee that an application will run.

.. warning::

   :code:`AppRun.c` (and its compiled binary in the AppImageKit releases) is legacy technology and should be avoided if possible. Using a modern :ref:`AppImage creation tool <appimage-creation-tools>` is strongly preferred; they made :code:`AppRun.c` obsolete in most cases.

   There are some edge cases where :code:`AppRun.c` might be useful and is still in use. However, it suffers from many limitations and requires some workarounds which themselves require troublesome mechanisms. For example, :code:`AppRun` force-changes the current working directory, and therefore applications cannot detect where the AppImage was originally called. This may be especially annoying for CLI tools, but can also be a problem for GUI applications expecting paths via parameters. This and other workarounds & mechanisms can cause a lot of trouble while trying to debug an AppImage. Please beware of that before thinking about using :code:`AppRun.c` in your AppImage.


Other
+++++

.. _ref-diricon:

``.DirIcon``
   The main application icon in the best resolution as PNG file. This is used, e.g. by thumbnailers, to display the application.

   The valid resolutions are |valid_resolutions|.

These two entries have been re-used from `ROX Filer`_'s specification. `ROX Filer`_ actually specifies additional (but optional) entries, however, AppImage doesn't use these. Instead, the following ones have been introduced:

``myapp.desktop``
   A :ref:`desktop entry file <desktop-entry-files>` located in the root directory, describing the payload application. As AppImage is following the principle :ref:`one app = one file <one-app-one-file-principle>`, one desktop file is enough to describe the entire AppImage. There |must not| be more than one desktop file in the root directory. The name of the file doesn't matter, as long as it carries the ``.desktop`` extension. The file can be a symlink to subdirectories such as ``usr/share/applications/...``

   For more information about desktop files, see :ref:`desktop-entry-files`.

.. _root-icon:

``myapp.<icon ext>`` (e.g., ``myapp.svg``, ``myapp.png``)
   The :ref:`application icon <icon-files>` in the best available quality, ideally a vector graphic. |supported_icon_formats|

   Can be a symlink to subdirectories such as ``usr/share/icons/hicolor/...``. In most cases, :ref:`.DirIcon <ref-diricon>` is a symlink to this file. The filename must be equal to what is set as ``Icon`` value in the desktop file.

   .. note::
      The ``Icon`` value |should not| contain the file extension, the actual file's filename however |should| carry the extension.

   For more information about icon files, see :ref:`icon-files`.


Conventions
-----------

In contrary to the rules in the previous section, the ones introduced in this section are no strict requirement. However, this is the recommended structure to put applications into AppDirs. It's picking up ideas from well-known, widely spread Linux standards such as the `Filesystem Hierarchy Standard`_ (part of the `Linux Standards Base`_).

.. seealso::
   A very good summary of the FHS can be found on `Wikipedia <https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard>`__.

.. _Filesystem Hierarchy Standard: https://wiki.linuxfoundation.org/lsb/fhs
.. _Linux Standards Base: https://wiki.linuxfoundation.org/lsb/start


``usr`` subdirectory
''''''''''''''''''''

Analog to the FHS, most AppDirs, especially the ones created by the official tools such as :ref:`linuxdeploy <ref-linuxdeploy>`, contain a ``usr`` directory.

``usr`` originally abbreviated *unix system resources*. According to the FHS, it contains shared, read-only data, which perfectly suits AppImage's needs, as AppImages are read-only, too.

The directory contains applications, (shared) libraries, desktop files, icons etc., in separate subdirectories:

``bin``
   Executables that can be called by a user.

``lib``
   (Shared) libraries used by applications in ``bin``.

``share``
   Architecture-independent (shared) data. Inside this directory, some special subdirectories are commonly placed:

   ``applications``
      Contains :ref:`desktop entry files <desktop-entry-files>` for applications in ``bin``. Normally, there's just one desktop file in this directory, which is symlinked in the root directory. For more information about desktop files, see :ref:`desktop-entry-files`.

   ``icons``
      Directory containing so-called `icon themes <https://standards.freedesktop.org/icon-theme-spec/icon-theme-spec-latest.html>`_. Contains at least one, but often a set of :ref:`icon files <icon_files>` for the main application. The icons are referred to by the root desktop file, which means the :ref:`same constraints <root-icon>` apply. The default theme is ``hicolor``, but icon files can also be adapted to other well-known themes to fit in better. Icon themes placed in this directory are copied to the system during so-called :ref:`desktop integration <ref-desktop-integration>`.

      Example path: ``<root>/usr/share/icons/<theme>/<resolution>/apps/myapp.<ext>``, e.g. ``<root>/usr/share/icons/hicolor/scalable/apps/myapp.svg`` or ``<root>/usr/share/icons/hicolor/512x512/apps/myapp.png``.

      For more information about icon files, see :ref:`icon-files`.


Summary
'''''''

The modern packaging tools such as :ref:`linuxdeploy <ref-linuxdeploy>` create these directories by default to standardize and harmonize AppDir creation. If you intend to :ref:`create AppDirs manually <ref-manual>`, you should follow these recommendations.


.. |must| replace:: **MUST**
.. |must not| replace:: **MUST NOT**
.. |should| replace:: **SHOULD**
.. |should not| replace:: **SHOULD NOT**

