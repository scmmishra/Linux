## Package Management Concepts

Contemporary distributions of Linux-based operating systems install software in pre-compiled *packages*, which are archives that contain binaries of software, configuration files, and information about dependencies. Furthermore, package management tools keep track of updates and upgrades so that the user doesn't have to hunt down information about bug and security fixes.

Without package management, users must ensure that all of the required dependencies for a piece of software are installed and up-to-date, compile the software from the source code (which takes time and introduces compiler-based variations from system to system), and manage configuration for each piece of software. Without package management, application files are located in the standard locations for the system to which the developers are accustomed, regardless of which system they're using.

Package management systems attempt to solve these problems and are the tools through which developers attempt to increase the overall quality and coherence of a Linux-based operating system. The features that most package management applications provide are:

-   **Package downloading**: Operating-system projects provide package repositories which allow users to download their packages from a single, trusted provider. When you download from a package manager, the software can be authenticated and will remain in the repository even if the original source becomes unreliable.
-   **Dependency resolution**: Packages contain metadata which provides information about what other files are required by each respective package. This allows applications and their dependencies to be installed with one command, and for programs to rely on common, shared libraries, reducing bulk and allowing the operating system to manage updates to the packages.
-   **A standard binary package format**: Packages are uniformly prepared across the system to make installation easier. While some distributions share formats, compatibility issues between similarly formatted packages for different operating systems can occur.
-   **Common installation and configuration locations**: Linux distribution developers often have conventions for how applications are configured and the layout of files in the `/etc/` and `/etc/init.d/` directories; by using packages, distributions are able to enforce a single standard.
-   **Additional system-related configuration and functionality**: Occasionally, operating system developers will develop patches and helper scripts for their software which get distributed within the packages. These modifications can have a significant impact on user experience.
-   **Quality control**: Operating-system developers use the packaging process to test and ensure that the software is stable and free of bugs that might affect product quality and that the software doesn't cause the system to become unstable. The subjective judgments and community standards that guide packaging and package management also guide the "feel" and "stability" of a given system.

In general, we recommend that you install the versions of software available in your distribution's repository and packaged for your operating system. If packages for the application or software that you need to install aren't available, we recommend that you find packages for your operating system, when available, before installing from source code.

The remainder of this guide will cover how to use specific package management systems and how to compile and package software yourself.

## Debian and Ubuntu Package Management

The Debian package management system, based on a tool called `dpkg` with the very popular `apt` system, is a powerful, popular, and useful method of package management. In addition to Debian, a number of other prominent distributions of GNU/Linux are derived from the Debian system, most notably the Ubuntu family of distributions.

As a result, these instructions apply to Debian and Ubuntu systems. While Debian and derived systems are not necessarily binary-compatible, `.debs` packaged for Debian are often compatible with Ubuntu (though this is not a supported workflow).

### Advanced Packaging Tool (APT)

You may already be familiar with `apt-get`, a command which uses the advanced packaging tool to interact with the operating system's package system. The most relevant and useful commands are (to be run with root privileges):

-   `apt-get install package-name(s)` - Installs the package(s) specified, along with any dependencies.
-   `apt-get remove package-name(s)` - Removes the package(s) specified, but does not remove dependencies.
-   `apt-get autoremove` - Removes any *orphaned* dependencies, meaning those that remain installed but are no longer required.
-   `apt-get clean` - Removes downloaded package files (.deb) for software that is already installed.
-   `apt-get purge package-name(s)` - Combines the functions of `remove` and `clean` for a specific package, as well as configuration files.
-   `apt-get update` - Reads the `/etc/apt/sources.list` file and updates the system's database of packages available for installation. Run this after changing `sources.list`.
-   `apt-get upgrade` - Upgrades all packages if there are updates available. Run this after running `apt-get update`.

While `apt-get` provides the most often-used functionality, APT provides additional information in the `apt-cache` command.

-   `apt-cache search package-name(s)` - If you know the name of a piece of software but `apt-get install` fails or points to the wrong software, this looks for other possible names.
-   `apt-cache show package-name(s)` - Shows dependency information, version numbers and a basic description of the package.
-   `apt-cache depends package-name(s)` - Lists the packages that the specified packages depends upon in a tree. These are the packages that will be installed with the `apt-get install` command.
-   `apt-cache rdepends package-name(s)` - Outputs a list of packages that depend upon the specified package. This list can often be rather long, so it is best to pipe its output through a command, like `less`.
-   `apt-cache pkgnames` - Generates a list of the currently installed packages on your system. This list is often rather long, so it is best to pipe its output through a program, like `less`, or direct the output to a text file.

Combining most of these commands with `apt-cache show` can provide you with a lot of useful information about your system, the software that you might want to install, and the software that you have already installed. If you're overwhelmed by `apt-cache` check out the following resources for easy-to-read lists of available packages:

-   [The Debian Package Directory](http://packages.debian.org)
-   [The Ubuntu Package Directory](http://packages.ubuntu.com)

### Aptitude

Aptitude is another front-end interface for APT. In addition to a graphical interface, Aptitude provides a combined command-line interface for most APT functionality. Some notable commands are:

-   `aptitude update`, `aptitude install`, `aptitude remove`, `aptitude clean`, or `aptitude purge` - Same as their apt-get counterparts.
-   `aptitude search` or `aptitude show`, - Same as their apt-cache counterparts.
-   `aptitude download` - Downloads a .deb file for a given package into the current directory.

Aptitude also includes *safe upgrading*, meaning it doesn't remove existing packages, as well as *holding*, which prevents the system from upgrading specific packages.

### /etc/apt/sources.list

The file `/etc/apt/sources.list` controls repositories from which APT constructs its database. This file contains lines in the following format:

    deb location-of-resources distribution component(s)

Here are some examples:

    deb http://mirrors.linode.com/debian/ jessie main contrib
    deb http://www.deb-multimedia.org jessie main non-free

The first line specifies the Linode mirror for the Debian 8 (code named Jessie) Linux distribution, as well as the main and contributed components. The next line specifies the deb-multimedia.org repository for Jessie, which provides some multimedia packages unavailable in the main repositories for licensing reasons, and its main and non-free components.

In general, one does not want to add new entries to `sources.list` without a lot of scrutiny and diligence, as updating the package cache with additional repositories and running upgrades can sometimes result in the installation of broken packages, unmet dependencies, and system instability. In Debian systems, downgrading is often difficult.

For Debian systems, the repository names can either refer to the distribution code name (e.g., jessie for current-stable, stretch for testing, sid for unstable, wheezy for old-stable) or to a specific branch (e.g., oldstable, stable, testing, unstable). For more information about Debian versions and choosing a Debian version or branch, read the [Debian releases and branches page](http://www.us.debian.org/releases/).

The component section of the line divides the repository based on how much support the developers of the operating system are able to offer for the contained packages (e.g. main vs. contrib), or if the software is considered "free-software" or simply freely-distributable (e.g., non-free).

The layout of `sources.list` is a bit different in Ubuntu systems. The lines are in the same format but the names of the distributions and components are different:

-   Ubuntu versions have a different naming scheme. Version 14.04 is named "trusty" in `sources.list`, 15.10 is "wily," and 16.04 is "xenial." These names follow an alphabetical pattern.
-   Ubuntu components are: "main" and "restricted" for supported free and non-free packages; "universe" and "multiverse" for unsupported free and non-free software.

### Using dpkg

`Apt-get` and `apt-cache` are merely frontend programs that provide a more usable interface and connections to repositories for the underlying package management tools called `dpkg` and `debconf`. These tools are quite powerful, and fully explaining their functionality is beyond the scope of this document. However, a basic understanding of how to use these tools is useful. Some important commands are:

-   `dpkg -i package-file-name.deb` - Installs a .deb file.
-   `dpkg --list search-pattern` - Lists packages currently installed on the system.
-   `dpkg --configure package-name(s)` - Runs a configuration interface to set up a package.
-   `dpkg-reconfigure package-name(s)` - Runs a configuration interface on an already installed package.

For information about building your own packages, refer to the [Debian New Maintainers Guide](http://www.debian.org/doc/maint-guide/).