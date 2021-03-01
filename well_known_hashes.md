#### Well Known Hashes

```hash
4b825dc642cb6eb9a060e54bf8d69288fbee4904

6ef19b41225c5369f1c104d45d8d85efa9b057b53b14b4b9b939dd74decc5321
```

##### Well Knwon Hash: Keccak or SHA3?

> Keccak:
```
c5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470
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


