---
title: Deterministic Build
time_fmt: `date -u +"%Y-%m-%dT%H:%MZ"`
tz: 2021-03-17T01:14Z
---

## debuerreotype

reproduciable debian images, this is what the offical docker images use

`https://download-directory.github.io/?url=https%3A%2F%2Fgithub.com%2Fdebuerreotype%2Fdocker-debian-artifacts%2Ftree%2Fdist-amd64%2Fbuster`

```bash
git commit -a --allow-empty-message -m ''
```

### npm `pack .tgz.`

```bash
https://github.com/npm/cli/commit/58d2aa58d5f9c4db49f57a5f33952b3106778669#diff-eabad0b45ec780b8b57ae6e3d47fd7d063680bea3acdc6b98a4b45ba3b808ccc
```

```diff
+        // Provide a specific date in the 1980s for the benefit of zip,
+        // which is confounded by files dated at the Unix epoch 0.
+        mtime: new Date('1985-10-26T08:15:00.000Z'),
```

` GIT_AUTHOR_DATE="1985-10-26T08:15:00.000Z" GIT_COMMITTER_DATE="1985-10-26T08:15:00.000Z" git commit --allow-empty -m 'Initial commit'`

### Git

```bash
$ GIT_AUTHOR_DATE="Thu, 01 Jan 1970 00:00:00 +0000" GIT_COMMITTER_DATE="Thu, 01 Jan 1970 00:00:00 +0000" git commit --allow-empty -m 'Initial commit'
```

```bash
GIT_AUTHOR_DATE="Thu, 01 Jan 1970 00:00:00 +0000" GIT_COMMITTER_DATE="Thu, 01 Jan 1970 00:00:00 +0000" git commit --allow-empty --allow-empty-message -m ''
```

### time2posix

IEEE Std 1003.1-1988 (``POSIX.1'') legislates that a time_t value of 536457599 shall
correspond to "Wed Dec 31 23:59:59 GMT 1986." This effectively implies that POSIX
time_t's cannot include leap seconds and, therefore, that the system time must be
adjusted as each leap occurs.

### RFC 822

| **RFC 822:**<br>_RFC 822 formatted date_             | Thu, 01 Jan 1970 00:00:00 +0000                  |
| ---------------------------------------------------- | ------------------------------------------------ |
| **ISO 8601:**<br>_ISO 8601 formatted date_           | 1970-01-01T00:00:00+00:00<br>1970-01-01 00:00:00 |
| **UNIX Timestamp:**<br>_seconds since Jan 1 1970_    | <br>00000000                                     |
| **Mac Timestamp:**<br>_seconds since Jan 1 1904_     |                                                  |
| **Microsoft Timestamp:**<br>_days since Dec 31 1899_ |                                                  |
| **FILETIME:**<br>_100-nanoseconds since Jan 1 1601_  | 0                                                |

### Git Tags

> be the primary artifact

[git-evtag](https://github.com/cgwalters/git-evtag)

Replacing tarballs

What git-evtag implements is an algorithm for providing a strong checksum over the complete source objects for the target:

```diff
-commit (- trees - blobs - submodules)
+commit (+ trees + blobs + submodules)
```

Then it's integrated with GPG for end-to-end verification. (Although, one could also wrap the checksum in X.509 or some other
public/private signature solution).

This is similar to what project distributors often accomplish by using git archive, or make dist, or similar tools to generate
a tarball, and then checksumming that, and (ideally) providing a GPG signature covering it.

If the checksum is not reproducible, it becomes much more difficult to easily and reliably verify that a generated tarball
contains the same source code as a particular git commit.

#### git-evtag

`$ git-evtag `

##### generate tag

```bash
$ git-evtag sign v2015.10
 ( type your tag message, note a Git-EVTag-v0-SHA512 line in the message )
$ git show v2015.10
 ( Note signature covered by GPG signature )
```

##### verify tag

```bash
$ git-evtag verify v2015.10
gpg: Signature made Sun 28 Jun 2015 10:49:11 AM EDT
gpg:                using RSA key 0xDC45FD5921C13F0B
gpg: Good signature from "Colin Walters <walters@redhat.com>" [ultimate]
gpg:                 aka "Colin Walters <walters@verbum.org>" [ultimate]
Primary key fingerprint: 1CEC 7A9D F7DA 85AB EF84  3DC0 A866 D7CC AE08 7291
    Subkey fingerprint: AB92 8A9C F8DD 0629 09C3  7BBD DC45 FD59 21C1 3F0B
Successfully verified: Git-EVTag-v0-SHA512: b05f10f9adb0eff352d90938588834508d33fdfcedbcfc332999ee397efa321d1f49a539f1b82f024111a281c1f441002e7f536b06eb04d41857b01636f6f268
```

### strftime()

The C and POSIX standards define for the strftime() function and the date utility
a notation for defining date and time representations.
Here are some examples, of how they can be used to produce ISO 8601 output:

```bash
format string 	output
%Y-%m-%d 	      1999-12-31
%Y-%j 	        1999-365
%G-W%V-%u      	1999-W52-5
%H:%M:%S      	23:59:59
```

### Reproducible Builds

```bash
export SOURCE_DATE_TZOFFSET = $(shell dpkg-parsechangelog -SDate | tail -c6)
```

`LC_ALL=C date -u -d '2015-10-21' `

In some cases, it is preferable to keep the original times for files that have
not been created or modified during the build process:

```bash
$ find build -newermt "@${SOURCE_DATE_EPOCH}" -print0 |
    xargs -0r touch --no-dereference --date="@${SOURCE_DATE_EPOCH}"
$ zip -r product.zip build
```

`--clamp-mtime` flag which will only set the time when the file is more recent
than the value specified with --mtime:

```bash
$ tar --mtime='2015-10-21 00:00Z' --clamp-mtime -cf product.tar build
```

#### Creating a Tarball

Works with GNU Tar 1.28

> note: Mac Users must use GNU Utils, as BSDTAR does not have some of these options. use `gtar` with `g` prefix denoting `GNU` core utils

```bash
$ tar --sort=name -cf product.tar build
```

For older versions or other archive formats, it is possible to use find and sort
to achieve the same effect:

```bash
$ find build -print0 | LC_ALL=C sort -z |
    tar --no-recursion --null -T - -cf product.tar
```

Tar offers a way to specify the user and group owning the file. Using `0/0` and
`--numeric-owner` is a safe bet, as it will effectively record 0 as values:

### Deterministic

<!-- Lyapunov -->

> Empty Directory is created and archived to be embedded within

```bash
$ mkdir -p build/.emptydir
$ tar --owner=0 --group=0 --numeric-owner -cf pkg.tar build/.emptydir
$ tar --owner=0 --group=0 --numeric-owner -cf product.tar build
```

Full example
The recommended way to create a Tar archive is thus:

```bash
$ tar --sort=name \
      --mtime="@${SOURCE_DATE_EPOCH}" \
      --owner=0 --group=0 --numeric-owner \
      --pax-option=exthdr.name=%d/PaxHeaders/%f,delete=atime,delete=ctime \
      -cf product.tar build
```

GNU ar and other tools from binutils have a deterministic mode which will use
zero for UIDs, GIDs, timestamps, and use consistent file modes for all files. It
can be made the default by passing the --enable-deterministic-archives option to
./configure

### Famous Timestamps

2009-01-13 12:46:54 PST == 2009-01-03 18:15:05 UTC
[https://web.archive.org/web/20130316013625/http://sourceforge.net/news/?group_id=244765](http://sourceforge.net/news/?group_id=244765)

#### Timetalbes

| **Text Date:Date in human-readable text**  | **Saturday, January 3, 2009 6:15:05 PM** |
| ------------------------------------------ | ---------------------------------------- |
| RFC 822:RFC 822 formatted date             | Sat, 03 Jan 2009 18:15:05 \+0000         |
| ISO 8601:ISO 8601 formatted date           | 2009\-01\-03T18:15:05\+00:00             |
| UNIX Timestamp:seconds since Jan 1 1970    | 1231006505                               |
| Mac Timestamp:seconds since Jan 1 1904     | 3313851305                               |
| Microsoft Timestamp:days since Dec 31 1899 | 39816\.76047                             |
| FILETIME:100\-nanoseconds since Jan 1 1601 | 12875480105000000001C96DCF:33B61A80      |

#### Well Known Hashes

```hash
4b825dc642cb6eb9a060e54bf8d69288fbee4904

6ef19b41225c5369f1c104d45d8d85efa9b057b53b14b4b9b939dd74decc5321
```

```bash
$ GIT_AUTHOR_DATE="Thu, 01 Jan 1970 00:00:00 +0000" GIT_COMMITTER_DATE="Thu, 01 Jan 1970 00:00:00 +0000" git commit --allow-empty -m 'Initial commit'
```

```diff
-rawsz sha1:20
+rawsz sha256:32

-hexsz sha1:40
+hexsz sha256:64

-zero sha1:0000000000000000000000000000000000000000
+zero sha256:0000000000000000000000000000000000000000000000000000000000000000

-algo sha1:sha1
+algo sha256:sha256

-empty_blob sha1:e69de29bb2d1d6434b8b29ae775ad8c2e48c5391
+empty_blob sha256:473a0f4c3be8a93681a267e3b1e9a7dcda1185436fe141f7749120a303721813

-empty_tree sha1:4b825dc642cb6eb9a060e54bf8d69288fbee4904
+empty_tree sha256:6ef19b41225c5369f1c104d45d8d85efa9b057b53b14b4b9b939dd74decc5321
```

The SHA2 `"6ef19b41225c5369f1c104d45d8d85efa9b057b53b14b4b9b939dd74decc5321"` is the new
SHA1 `"4b825dc642cb6eb9a060e54bf8d69288fbee4904"` empty tree.
