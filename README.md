
# Simple tools for virtual hard-drives

This is a small library of simple tools for creating and working with virtual hard-drives in Linux.

Virtual hard-drives (VHDs) are ordinary files that are formatted internally as file-systems.
They can be attached to a loop-device (`/dev/loopX`), and mounted to a folder as a normal file-system using `mount`.

Mounting/unmounting operations require root privileges, so the following tools will prompt you for your password:
```
vhd-format vhd-mount vhd-umount
```
Note that this has nothing to do with the read/write permissions of the mounted filesystem; these permissions depends on the mount options, which themselves depend on the file-system type (eg EXT4 and NTFS have different options). You can read more about these options [here](https://www.computerhope.com/unix/umount.htm).

## Installation

There is no specific installation required, simply add the folder to your `PATH`, by adding the following line to your `.bash_profile`:
```
export PATH="/path/to/folder":$PATH
```

Note that this library depends on the following Linux tools
```
du df mkfs truncate losetup mount findmnt fuser
```
which are part of the following Linux packages
```
coreutils util-linux psmisc e2fsprogs
```
You should make sure these packages are installed on your system prior to using this library.


## Documentation

This is a short example of how these tools can be used:
```bash
# create temporary folder
mkdir -p /tmp/foo/test
cd /tmp/foo

# create new VHD of 50M, and format it as NTFS
vhd-create test.vhd 50M
vhd-format test.vhd ntfs

# mount it and create a file in it
vhd-mount test.vhd test/
echo "Hello World!" >> test/example

# show info and unmount
vhd-info test.vhd
vhd-umount test.vhd
```

#### Create an empty VHD

The first step is to create a file of a certain size.
After this step, the VHD is still a normal file, and in particular it is NOT formatted.

```
vhd-create <filepath> <size>

    <filepath> 
    Can be an absolute (`/path/to/file`) or relative path (`../folder/file`).
    It is good practice to specify the extension `.vhd` for virtual hard-drives.

    <size>
    Size in bytes, supporting human-readable formats (eg `10M` or `1.2G`).
    Note that this size will actually be allocated, so be careful not to fill up your drive.
```

#### Format an empty VHD

This is where the previously allocated file becomes an actual filesystem.
Note that the VHD is attached temporarily to a loop-device during this operation, but the device is released automatically after formatting.

> WARNING!
> This operation requires root privileges, and will ERASE ALL CONTENTS in the file.

```
vhd-format <filepath> <format>

    <filepath>
    Absolute or relative path to a VHD created with vhd-create.

    <format>
    One of the supported format by mkfs.
    To see all available format, type 'mkfs.' in a terminal, and see all available completions (by pressing TAB twice).
```

#### Mount or unmount a formatted VHD

Formatted VHDs can be mounted/unmounted as normal filesystems.

```
vhd-umount <filepath>
vhd-mount  <filepath> <folder> <options...>

    <filepath>
    Absolute or relative path to a VHD file formatted with 'vhd-format'.

    <folder>
    Absolute or relative path to an existing folder.
    The permissions granted to this folder may be ignored by mount (see mounting options).

    <options...>
    All subsequent arguments as forwarded to the command 'mount'.
```

#### Information about a mounted VHD

This command provides information about storage space, mounting options, and active processes using the attached device.

```
vhd-info <filepath>

    <filepath>
    Absolute or relative path to a VHD file mounted with 'vhd-mount'.
```

## License

These tools are released under MIT license.
The original author is Jonathan Hadida ([github/sheljohn](https://github.com/sheljohn)).
