# Small Miscellaneous Fixes #

Any miscellaneous / smaller fixes go here 

## Google Chrome Install Error ##
*From https://www.linuxquestions.org/questions/linux-software-2/messed-up-dpkg-google-chrome-stable-package-is-in-a-very-bad-inconsistent-state-4175498370/*

After installing chrome's stable release, something broke and I couldn't remove the package.

When trying to remove chrome, there was an error similar to this one
```
xdg-icon-resource: size argument must be numeric
```

Files were left behind from the install, and the standard `--remove` and `--force-remove` commands weren't working

To fix this, run the following find/grep command:
```
sudo find /var/lib/dpkg -exec grep -l "google-chrome-stable" {} \;
```

Then open up `/var/lib/dpkg/info/google-chrome-stable.prerm` (will require root)

Add `exit 0` as the first line for this file, then run the following to purge chrome:

```
sudo dpkg -P google-chrome-stable
```
