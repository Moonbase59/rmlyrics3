# rmlyrics3

A **_Lyrics3_ tag remover** for MP3 files, written in Python.

Before ID3v2 came around, [Lyrics3](http://id3.org/Lyrics3) and [Lyrics3v2](http://id3.org/Lyrics3v2) had their uses (extending the 30-char limit, placing synced lyrics), but nowadays they are a nuisance for most of us. Plus, we have the lyrics frames `USLT` and `SYLT` in ID3v2.

_Lyrics3_ tags come between the audio data and an _ID3v1/ID3v1.1_ tag at the end of the file (sometimes without the following ID3v1 tag, even if that is mandatory according to Lyrics3 specs). Current ffmpeg and some players and tools still have bugs and try to interpret Lyrics3 tags as audio data, resulting in obscure errors. (See https://github.com/Moonbase59/loudgain/issues/11 or https://trac.ffmpeg.org/ticket/7879.)

Unfortunately, there are almost no tools out there to **remove** Lyrics3 tags—most programs ignore them, but write them back if changing tags. When removing ID3v1 tags from a file, this can result in the illegal situation mentioned above (having a Lyrics3 tag without a following ID3v1 tag).

`rmlyrics3` can remove _Lyrics3_ and _Lyrics3v2_ tags from MP3 files, even when there is no ID3v1 tag following.

This code has been tested with thousands of files but I can give no guarantees. If you destroy your whole music collection, it’s not my fault. **Please have a backup!**

## Installation

### Install on OSes that have Python installed

Just copy `rmlyrics3` to a suitable location (i.e., `~/bin` or `/usr/local/bin`) and make it executable (`chmod +x rmlyrics3`).

All other files are _not needed_ for normal operation. These are mainly building and unit testing scripts.

### Install on Windows with no Python installed

There is a Windows 32-bit executable `rmlyrics3.exe` in the `dist/` folder which you can copy to a suitable location. It should run on Windows 7 and above.

The prebuilt Windows executable is meant to be used on systems with no Python installed. It brings its own Python interpreter and needed dependencies. Thus, it’s a little larger and takes a little longer to start than the pure Python version.

### Rebuilding the Windows executable

Should you need to rebuild `rmlyrics3.exe` yourself, get [PyInstaller](https://www.pyinstaller.org/), change to the directory into which you have git cloned _rmlyrics3_ and do a:

```bash
pyinstaller rmlyrics3.spec
```

## Usage

This is also what you get when you type `rmlyrics3 -h` or `rmlyrics3 --help`:

    usage: rmlyrics3 [-h] [-v] [-r] [-d] path [path ...]

    Remove Lyrics3 tags from MP3 files.

    positional arguments:
      path                  Path of a file or a folder of files.

    optional arguments:
      -h, --help            show this help message and exit
      -v, --version         show program's version number and exit
      -r, --recursive       search through subfolders (default: False)
      -s, --stats           show statistics (default: False)
      -d, --dry-run, --dryrun
                            dry run; files not changed (default: False)

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

## Technical

_rmlyrics3_ tries to segment the MP3 file into _blocks_. It knows about the following block types:

* ID3v1, ID3v1.1, ID3v2.2, ID3v2.3, ID3v2.4, APEv1, APEv2, Lyrics3, Lyrics3v2, and audio data.

For safety reasons, rmlyrics3 proceeds as follows:

1. If more than one file is to be processed, build a list of file paths and names and sort them alphabetically. This makes it easier to search for a specific file in the output.

2. Read the original file (say, `test.mp3`), determine its structure and possibly show an error if the file is corrupt.

3. Determine if the file contains a _Lyrics3_ or _Lyrics3v2_ tag and skip the following steps if not so.

4. Rebuild a new copy of the file in the same folder, adding a `.tmp` extension (`test.mp3` → `test.mp3.tmp`).

5. Display the path and file name and blocks found:
   ```bash
   rmlyrics3 test.mp3
   /home/user/Music/Test/test.mp3 ... (ID3v2, Audio, APEv2, Lyrics3v2, ID3v1)
   ```

   You can easily see that this file contains an _ID3v2_ tag at the beginning, followed by the audio data, followed by an _APEv2_ tag, followed by a _Lyrics3v2_ tag, and ending with an _ID3v1_ tag. (After removal of the Lyrics3v2 tag, the sequence will be: ID3v2, audio data, APEv2, ID3v1.)

   rmlyrics3 will _only show files that are changed_. This is an extra piece of safety since you can redirect its output to a file and later check if problems should arise:
   ```bash
   rmlyrics3 test.mp3 > changed-files.txt
   ```

6. Finally, rmlyrics removes the original file (`test.mp3`) and renames the newly-built copy to the original’s name (`test.mp3.tmp` → `test.mp3`).

7. Proceed with next file, if any.

It could be argued to _truncate_ the original file instead and rewrite part(s) of it, but this is not always a safe procedure (and _truncate_ not being supported on all OSes), so I opted for the above strategy.

Should you get **warning** or **error messages** in the process, your file is most probably **defective** and has unexpected junk data inside. There are many more bad MP3 files out there than you might suspect. I **strongly** advise to **delete** such bad files or try to **repair them** using [MP3 Diags](http://mp3diags.sourceforge.net/), if you know what you’re doing.

_Best of luck cleaning out your music files!_
