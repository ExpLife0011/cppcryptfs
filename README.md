![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)
==============

cppcryptfs
------

This software is based on the design of [gocryptfs](https://github.com/rfjakob/gocryptfs), an encrypted overlay filesystem written in Go.

cppcryptfs is an implementation of gocryptfs in C++ for Windows.

It uses the the [Dokany](https://github.com/dokan-dev/dokany) driver and library to provide a virtual fileystem in user mode under Windows.


Current Status
--------------

cppcryptfs is best described as currently pre-alpha, or more accurately: EXPERIMENTAL.


Testing
-------

cppcryptfs seems to work.  It passesss 169/171 of the tests in [winfstest](https://github.com/dimov-cz/winfstest).

The two failures are due to file sharing issues.  Due to the nature of how gocryptfs is designed (namely, that it is impossible to write to a file unless you are able to also read from it), it is probably impossible to pass these two tests.  And it is the opinion of the developer that these failures probably don't matter.

Build Requirements
-------
	
	Microsoft Visual Studio 2015 Community Edition
	openssl - https://github.com/openssl/openssl (static build recommended)
	rapidjson - https://github.com/miloyip/rapidjson (for parsing gocryptfs.conf)
	dokany - https://github.com/dokan-dev/dokany

	For Dokany, you probably want to get the binary distribution and install it from here:
		https://github.com/dokan-dev/dokany/releases

	The version currently used with cppcryptfs is Dokany 1.0.0-RC3

Use
-------

cppcryptfs needs to run as administrator.  It needs this to acquire the SE_NAME privilege in Windows for it to work.

cppcryptfs.exe requests administrator privileges automatically which 
pops up the UAC dialog.

Admin privilege is specified in the manifest.  A consequence of this is that
in order to debug or even run it from Visual Studio, you need to run
Visual Studio as administrator.

To make a new encrypted virtual fileystem, first click the "Create" tab.

You need to find or create (you can create a directory in the directory selector in the UI) an empty directory to be the root of your filesystem.

It is strongly recommended that this directory reside on an NTFS filesystem.

Then you need to choose a (hopefully strong) password and repeat it.

When you click on the "Create" button, a gocyrptfs.conf file will be created in the directory, as will a gocryptfs.diriv.  Be sure to backup these files in case they get lost or corrupted.  You won't be able to access any of your data if something happens to gocryptfs.conf.  gocryptfs.conf will never change for the life of your filesystem.

Then go to the "Mount" tab and select a drive letter and select the folder you
just created the filesystem in.  Then enter the password and click on the "Mount" button.

Your will then have a new drive letter, and you can use it like a normal drive letter and store your sensitive information there.  The data is encrypted and saved in files in the folder you specified.

The files are encrypted using AES256-GCM, and the filenames are encrypted using
AES256-EME (by default).  When you create a filesystem, you can choose to use plain text filenames or AES256-CBC encryption for filenames if you wish.


When you are finished using the drive letter, then go to the "Mount" tab and click on "Dismount" or "Dismount All".  The drive letter(s) will be unmounted, and the encryption keys will be erased from memory. 

cppcryptfs uses VirtualLock() to prevent encryption keys from ending up in the paging file.  If you never hibernate your computer, then you don't have to worry about the keys being written to the disk.

If you minimize cppcryptfs, then it will hide itself in the system tray.
