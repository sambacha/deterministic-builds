---
title: Deterministic Build - TimeZones
version: draft

---

> Additional Details

#### TimeZones

```bash
date -u +"%Y-%m-%d %H-%M-%SZ" 
date -u +"%Y-%m-%dT%H:%MZ"
```
Z and +00:00 are the same (mostly). For purposes of translating time, they both mean UTC. 
However England is +00:00 in winter and +01:00 in summer (BST).
Z is UTC, while +00:00 is GMT.

GNU `date` `date -I` is the same as ` date +%F`, and `-Iseconds` and `Iminutes` also include time with UTC offset.

```bash
date +%F # -I or +%Y-%m-%d
date +%FT%T%z # -Iseconds or +%Y-%m-%dT%H:%M:%S%z
date +%FT%H:%M # -Iminutes or +%Y-%m-%dT%H:%M%z
date -u +%FT%TZ
```
 

#### Valid ISO 8601 date or time formats

```bash
20130503T15 (%Y%m%dT%M)
2013-05 (%Y%m)
2013-W18 (%Y-W%V)
2013-W18-5 (%Y-W%V-%u)
2013W185 (%YW%V%u)
2013-123 (%Y-%j, ordinal date)
2013 (%Y)
1559 (%H%M)
15 (%H)
15:59:24+03 (UTC offset doesn't have to include minutes)
````
```bash
$ date -u -Iseconds
$ date -u '+%Y-%m-%dT%k:%M:%S%z'
```