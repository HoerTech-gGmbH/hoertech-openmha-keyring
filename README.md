# hoertech-openmha-keyring
Creates a Debian package which installs the apt key that is used to sign
our apt repositories at http://apt.hoertech.de and http://aptdev.hoertech.de.
The Debian package itself is only uploaded to http://apt.hoertech.de, but
the signing key is the same for both repositories.

Our users do not need to install this package, they can instead follow the
project-specific installation instructions. This package is needed for
mahalia multistrap builds.

The key needs to be stored in format "GPG key public ring".
This is a binary format, not the ascii-armored format
that is usually used to exchange keys.

To create a key file in the correct binary format, export it from gnupg
with `gpg --export`, store it in a file with filename extension `.gpg`,
and ask the `file` utility if the key is in correct format:

```
$ file *.gpg
openmha-packaging-2018.gpg: GPG key public ring, created ...
```

The Jenkinsfile builds the Debian package with the help of `mhamakedeb`.
Then, it triggers an upload of the package to apt.hoertech.de.

Jenkins job /hoertech-tools/hoertech-openmha-keyring was configured to execute
this Jenkinsfile.

The openmha packaging key from 2018 expires 2022-05-16.
Add a new key before then, as a file and in file hoertech-openmha-keyring.csv,
and update the version in file `version` to reflect the year when the
new key was created. The old key can be removed when it has expired.
BE CAREFUL, THIS GIT REPOSITORY IS STORED PUBLICLY ON GITHUB.  NEVER UPLOAD
THE PRIVATE PART OF A GPG KEY TO GITHUB.

The debian package installs the key in directory `/etc/apt/trusted.gpg.d`
where it will be picked up by apt automatically.
