# hoertech-openmha-keyring
Creates a Debian package which installs the apt key that is used to sign
our apt repositories at http://apt.hoertech.de and http://aptdev.hoertech.de.
The Debian package itself is only uploaded to http://apt.hoertech.de, but
the signing key is the same for both repositories.

Our signing key has time-limited validity.  Other packages in the apt
repositories apt.hoertech.de and aptdev.hoertech.de may depend on this
package so that our users can benefit from automatic upgrades of the
signing key before it expires.

This package is also needed for mahalia multistrap builds.

The key needs to be stored in format "GPG key public ring".
This is a binary format, not the ascii-armored format
that is usually used to exchange keys.

To create a key file in the correct binary format, export it from gnupg
with `gpg --export`, store it in a file with filename extension `.gpg`,
and ask the `file` utility if the key is in correct format:

```
$ file *.gpg
openmha-packaging-2022.gpg: GPG key public ring, created ...
```

The Jenkinsfile builds the Debian package with the help of `mhamakedeb`.
Then, it triggers an upload of the package to apt.hoertech.de.

Jenkins job /Software Packaging and Distribution/hoertech-openmha-keyring
was configured to execute this Jenkinsfile.

The openmha packaging key from 2022 expires 2025-02-21.
Add a new key before then, as a file, and update the key file name in files
hoertech-openmha-keyring.csv and hoertech-openmha-keyring.postinst.
Also update the version in file `version` to reflect the year when the
new key was created. The old key can be removed when it has expired.
BE CAREFUL, THIS GIT REPOSITORY IS STORED PUBLICLY ON GITHUB.  NEVER UPLOAD
THE PRIVATE PART OF A GPG KEY TO GITHUB.

The debian package installs the key in directory `/usr/share/keyrings`,
where it expected by multistrap.

The postinstall script of this debian package calls `apt-key add` on the
key, thereby adding it to the apt database, updating public keys which
expire earlier.
