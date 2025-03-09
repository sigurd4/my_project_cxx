# my_project_cxx

A pre-made cross-platform workspace for easily writing C++23. This is just a project template.

## What is this?

This is a cross-platform workspace that is set-up with CMake to do the following:
- Allow use of C++20/C++23 modules
- Allow import of the standard library as a module `std`, even though this is not yet (as of writing) officially supported by CMake.
- Automatically link and compile source, header and module files with recursive scanning, inside the `src`-folder.
- Automatically compile tests in the `tests`-folder and run them with CTest.
- Minimum setup required, if the instructions here are followed, it should (hopefully) work almost right out of the box.

The reason i made this was so that students taking a course in modern C++23, who are not very familiar with CMake, can more easily do their homework.

## Support

I'm using this setup on Arch-Linux, and it works great on there.

I also tried it on MacOS Monterey in a VM, and it worked more or less. I did get some linker problems with parts of the standard library, but i suspect this is because i was running MacOS on x86_64 architecture in a virtual machine. There's a good chance it works perfectly fine on a system with ARM architecture.

I've not yet tried this on Windows, and as such the setup is incomplete.

## Setup

### Install prerequesites (any IDE)

For this workspace to function, you need to install CMake, Ninja, Clang, LLD, and libc++. You also need git to clone this repository.

#### Windows

On Windows, libc++ is not available. I haven't set this up for Windows yet, but if i were to try to do it, you'd need a different standard library and would probably not be able to do `import std` until CMake supports this officially (supposedly in CMake version 3.30). Any help with making this Windows compatible is greatly appreciated.

Git can be installed from this webpage: https://git-scm.com/downloads.

You can get most of the prerequesites (except `libc++` and i'm not sure about `lld`) from the Visual Studio Tools Installer, from here: https://visualstudio.microsoft.com/downloads/. 

#### Arch-Linux (and possibly other distros that use pacman)

For convenience, you might want an AUR wrapper, for example `yay` or `paru`. If not, you'll need to clone the repo of each AUR package and run `makepkg -si` inside.

Install the following packages from the AUR:
- `git`
- `cmake`
- `ninja`
- `llvm-minimal-git` (Don't try to build `llvm-git`. You will run out of disk space. Trust me, i have tried.)
- `libc++abi`

On other distributions, you need the equivalent of these packages, and the rest should be easy.

#### MacOS/OSX

Clang comes pre-installed on MacOS, but it's not a new enough version. For this setup i've picked the (at the time of writing) newest version of Clang, `clang-19`. If you later were to use a newer version, the `CMakeLists.txt` will need changes.

1. Install XCode Command Line Developer Tools
  1. Open up the terminal
  2. `xcode-select --install` (this includes `git`, and is also a requirement for MacPorts to work)
2. Install MacPorts (alternatively, Homebrew can be used)
  1. Go to this webpage: https://www.macports.org/install.php
  2. Select the correct download link for your version of MacOS
  3. An installer will be downloaded. Run this and you're set.
4. On MacOS, the environment variables needed to run a C/C++ compiler are not set by default. Set them in the file ´~/.bashrc´
  1. Open up the terminal
  2. `touch ~/.bashrc`
  3. `open ~/.bashrc` (A text editor will now open)
  4. In the text editor, add these lines to the bottom of the file:
```bash
export CC=clang
export CXX=clang++
export CMAKE_C_COMPILER=clang
export CMAKE_CXX_COMPILER=clang++
export CXXFLAGS="-cxx-isystem/opt/local/libexec/gcc11/libc++/include/c++/v1"
export CPLUS_INCLUDE_PATH=/opt/libexec/gcc11/libc++/include/c++/v1
export CLANG_DEFAULT_CXX_STDLIB=libc++
```
  5. Verify that the path to the standard library is correct on your system. If you do `ls /opt/local/libexec/gcc11/libc++/include/c++/v1`, you should see some header files (`.h`), like `stddef.h`, `stdio.h`, and so on. If this is not the case, figure out the correct path to the `libc++` standard library and use that path instead.
  6. Save the file `~/.bashrc` (⌘ + S) and close the text-editor window
4. Now you are ready to install a few packages with MacPorts. If you use Homebrew, the command is different, but you still need the equivalent packages. The default shell `zsh` does not use the `~/.bashrc` file, so you need to change shell to `bash` first.
  1. `bash` (This enters the shell `bash` instead of `zsh`. Otherwise, the environment variables we set will not be used.)
  2. `sudo port install cmake`
  3. `sudo port install ninja`
  4. `sudo port install procs`
  5. `sudo port install clang-19`
  6. If any of these fail, check the log file and figure out the problem. Enter `sudo port clean <name of package>` to cleanly rebuild the package after you've done changes elsewhere. If all goes well, you now have all the right prerequesites installed.

### Setting up Visual Studio Code

1. Install VSCode or any equivalent (VSCodium, ...).
  - Arch-Linux: `sudo pacman -S code`
  - Windows/MacOS: Download the installer at https://code.visualstudio.com/.
2. If you haven't already, make a folder for your C++ workspaces. (For example, `~/Code/c++/`)
3. Clone this git repo by entering `git clone https://github.com/sigurd4/my_project_cxx.git` in the terminal while in this directory you just made.
4. Open VSCode in the new folder.
5. A prompt asking you to install recommended extensions might shop up. Click yes, or install them individually in the extensions panel under the `Recommended` tab. Wait for the installation to complete.
6. A new toolbar at the bottom of the screen will appear (from the CMake Tools extension). Click the button that says `No kit selected`, then click `Scan for kits` in the pop-up menu.
7. Click the `No kit selected` button again, and select the newest version you can find of `clang` (not `clang-cl`).

 #### Setting up Clangd in VSCode

For Clangd to function better, it is recommended to add the following lines to your settings.js. (Open it by pressing Ctrl + Shift + P or ⌘ + Shift + P to open the run menu, then type "settings json" and you should see the correct option `Preferences: Open User Settings (JSON)`).

##### Linux
 
```json
    "clangd.path": "/usr/bin/clangd",
    "clangd.arguments": [
        "--query-driver=/usr/bin/clang,/usr/bin/clang++",
        "--target=x86_64-pc-linux-gnu",
        "--background-index=0"
    ]
```

The target triple might possibly vary from system to system.

##### Others

Same as with Linux, but change the paths and the target triple to whatever is correct on your system. My guess would be that `clangd` lies in the same directory as `clang`.
