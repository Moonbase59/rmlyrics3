# rmlyrics3

A **_Lyrics3_ tag remover** for MP3 files, written in Python.

Before ID3v2 came around, [Lyrics3](http://id3.org/Lyrics3) and [Lyrics3v2](http://id3.org/Lyrics3v2) had their uses (extending the 30-char limit, placing synced lyrics), but nowadays they are a nuisance for most of us. Plus, we have the lyrics frames `USLT` and `SYLT` in ID3v2.

_Lyrics3_ tags come between the audio data and an _ID3v1/ID3v1.1_ tag at the end of the file (sometimes without the following ID3v1 tag, even if that is mandatory according to Lyrics3 specs). Current ffmpeg and some players and tools still have bugs and try to interpret Lyrics3 tags as audio data, resulting in obscure errors. (See https://github.com/Moonbase59/loudgain/issues/11 or https://trac.ffmpeg.org/ticket/7879.)

Unfortunately, there are almost no tools out there to **remove** Lyrics3 tags—most programs ignore them, but write them back if changing tags. When removing ID3v1 tags from a file, this can result in the illegal situation mentioned above (having a Lyrics3 tag without a following ID3v1 tag).

`rmlyrics3` can remove _Lyrics3_ and _Lyrics3v2_ tags from MP3 files, even when there is no ID3v1 tag following.

This code has been tested with thousands of files but I can give no guarantees. If you destroy your whole music collection, it’s not my fault. **Please have a backup!**

## Installation

Just copy `rmlyrics3` to a suitable location (i.e., `~/bin` or `/usr/local/bin`) and make it executable (`chmod +x rmlyrics3`).

The file `test_rmlyrics3.py` is a developer’s unit test. It is _not needed_ for normal operation.

## Usage

This is also what you get when you type `rmlyrics3 -h` or `rmlyrics3 --help`:

    usage: rmlyrics3 [-h] [-v] [-r] [-d] path [path ...]

    Remove Lyrics3 tags from MP3 files.

    positional arguments:
      path                  Path of a file or a folder of files.

    optional arguments:
      -h, --help            show this help message and exit
      -v, --version         show program's version number and exit
      -r, --recursive       Search through subfolders (default: False)
      -d, --dry-run, --dryrun
                            Dry run; files not changed (default: False)

## Examples

Remove Lyrics3 tag from a single file:
```bash
rmlyrics3 test.mp3
```

Remove Lyrics3 tags from a collection of files:
```bash
rmlyrics3 test1.mp3 test2.mp3 test3.mp3
```

Remove Lyrics3 tags from all MP3 files in a folder:
```bash
rmlyrics3 *.mp3
```

Remove Lyrics3 tags from all MP3 files in a folder **and all its subfolders** (recursively):
```bash
rmlyrics3 -r ~/Music/MyMP3/
```

For a **dry run** (without actually changing files), use the `--dry-run` (`-d`) switch:
```bash
rmlyrics3 --dry-run -r ~/Music/MyMP3/
```
