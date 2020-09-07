# hoertech-openmha-keyring
Creates debian package for hoertech-openmha-keyring

The package needs to be stored in format
"GPG key public ring".  This is a binary format, not the ascii-armored format
that is usually used to exchange keys.

Ask the file utility if the key is in correct format.

The Jenkinsfile builds the debian package with the help of `mhamakedeb`.
Then, it triggers an upload of the package to apt.hoertech.de.
