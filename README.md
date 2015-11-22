Raspple II Developers GnuPG keyring
===================================

To ensure that the Raspple II packages and their archive are less likely
to be compromised, they are digitally signed with a program called GnuPG.
It is the modern equivalent of a program called Pretty Good Privacy (PGP).

This package contains the public keyrign for Raspple II developer keys.
Once you install this package, apt will no longer issue warnings about
insecure packages and repositories unless something has been tampered
with.

This package includes two keyring files and operates using the apt-key
command.  The first keyring, rasppleii-keyring, contains keys added keys
that are valid for Raspple II packages and archives to use and should be
added to apt.  The second keyring, rasppleii-keyring-remove, contains any
keys that should be removed from apt and no longer used.

Information about apt's archive authentication can be found here:
<http://wiki.debian.org/SecureApt/>
