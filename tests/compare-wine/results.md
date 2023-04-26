
# Setup

Prepare game directory:

```
# install EET and hq_soundclips_bg2ee
$ modda install -m /home/mick/Games/dev/eet-modda/tests/compare-wine/prepare-base.yml
[...]
# get and extractall mod archives
$ modda install -m /home/mick/Games/dev/eet-modda/tests/compare-wine/compare-wine.yml --dry-run
```

Note: Did that with native weidu in both case, it shouldn't change the result for the second phase.

```
real    3m43,360s
user    2m50,379s
sys     0m49,493s

```

# Native weidu on native install

Native bg2 install in ext4 with casefolding

```yaml
archive_cache: ~/Games/modda-cache
extract_location: ~/Games/installs/baldur/tmp
extractors:
    rar:
        command: unrar-nonfree
        args: [ "x", "${input}", "${target}" ]
    7z:
        command: 7z
        args: [ "x", "${input}", "-o${target}" ]
```

```
$ time modda install -m $COMPARE_MODDA_PATH/compare-wine.yml
[...]
real    21m27,013s
user    16m7,106s
sys     5m15,611s
```

# Wine weidu on native install (take 1)

```yaml
weidu_path: ~/bin/weidu-wine.sh
```
`weidu-wine.sh` is
```sh
#!/usr/bin/sh
wine ~/bin/weidu.exe "$@"
```

First impression: slow!
At the 30min mark, ascension is installed..

After iwdification, program was interrupted:

```
NOT INSTALLED DUE TO ERRORS IWD Arcane Spell Pack

SUCCESSFULLY INSTALLED      IWD Divine Spell Pack
[2023-04-25T10:51:37Z INFO  modda::process_weidu_mod] module iwdification (index=2/68) finished with error (status=2), stopping.
Error: Program interrupted on error on non-whitelisted warning

real    48m15,691s
user    0m0,415s
sys     0m0,427s
```

_48 minutes_ :O

Then going on:

```
$ time modda install -m /home/mick/Games/dev/eet-modda/tests/compare-wine/compare-wine.yml --from-index 3
[...]
NOT INSTALLED DUE TO ERRORS Ascension: Turnabout

NOT INSTALLED DUE TO ERRORS Balthazar Epilogue Portrait, by Cliffette
[2023-04-25T12:21:26Z INFO  modda::process_weidu_mod] module turnabout (index=28/68) finished with error (status=2), stopping.
Error: Program interrupted on error on non-whitelisted warning

real    45m41,797s
user    0m5,280s
sys     0m4,411s
```

Hm... I may have made an error, weidu-wine runs as a _windows_ program so `HANDLE_CHARSETS` (and maybe other things) need the windows version, with iconv and things like that

Looking a little more, `HANDLE_AUDIO/HANDLE_TILESET` too.

It all boils down to the `WEIDU_OS`. Well I must not use `.iemod` or `lin-*` archives :D

# Wine weidu on native install (take 2)

Using a duplicate `compare-wine-win.yml`,with iemod and linux archives replaced with github tags.

```
modda install -m /home/mick/Games/dev/eet-modda/tests/compare-wine/compare-wine-win.yml --dry-run
time modda install -m /home/mick/Games/dev/eet-modda/tests/compare-wine/compare-wine-win.yml
```

- starts at 15:06
- ascension is done at 15:32 (26 minutes)
- 16:28 thecalling isdone
- 16:45 C#SODBOABRI (1:39 14/68)
- 17:00 bst (1:54 23/68)
- 17:08 cowledmenace (2:02 28/68)

interrupted at npckits because of missing iconv

```
real    127m8,662s
user    0m6,915s
sys     0m6,016s
```
