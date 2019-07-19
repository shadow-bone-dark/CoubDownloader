# CoubDownloader

CoubDownloader is a simple script to download videos (called coubs) from [Coub](https://coub.com).  

## coub.sh vs. coub.py

Both versions are standalone scripts. You can use either and get the same functionality (with the exception of `--limit-rate` only being available with coub.sh).

So what version to use?

The answer is: **coub.py**

coub.py is supposed to work on all platforms, has less requirements and depending on the performed action*, it is faster than coub.sh.

**Channel parsing profits the most, requiring 25-40% less time for ~1000-3000 parsed links. Tag parsing is mostly limited by Coub throttling the frequency of API calls after a while, so benefits are small compared to channel parsing (~7% less time for 2475 links). For the actual download of coubs the speed difference is negligible.* 

## Usage

```
CoubDownloader is a simple download script for coub.com

Usage: coub.sh [OPTIONS] INPUT [INPUT]... [-o FORMAT]

Input:
  LINK                   download specified coubs
  -l, --list LIST        read coub links from a text file
  -c, --channel CHANNEL  download all coubs from a channel
  -t, --tag TAG          download all coubs with the specified tag
  -e, --search TERM      download all search results for the given term

Common options:
  -h, --help             show this help
  -q, --quiet            suppress all non-error/prompt messages
  -y, --yes              answer all prompts with yes
  -n, --no               answer all prompts with no
  -s, --short            disable video looping
  -p, --path PATH        set output destination (default: '.')
  -k, --keep             keep the individual video/audio parts
  -r, --repeat N         repeat video N times (default: until audio ends)
  -d, --duration TIME    specify max. coub duration (FFmpeg syntax)

Download options:
  --sleep TIME           pause the script for TIME seconds before each download
  --limit-rate RATE      limit download rate (see curl's --limit-rate)
  --limit-num LIMIT      limit max. number of downloaded coubs
  --sort ORDER           specify download order for channels/tags
                         Allowed values:
                           newest (default)      likes_count
                           newest_popular        views_count
                           oldest (tags/search only)

Channel options:
  --recoubs              include recoubs during channel downloads (default)
  --no-recoubs           exclude recoubs during channel downloads
  --only-recoubs         only download recoubs during channel downloads

Preview options:
  --preview COMMAND      play finished coub via the given command
  --no-preview           explicitly disable coub preview

Misc. options:
  --audio-only           only download audio streams
  --video-only           only download video streams
  --write-list FILE      write all parsed coub links to FILE
  --use-archive FILE     use FILE to keep track of already downloaded coubs

Output:
  -o, --output FORMAT    save output with the specified name (default: %id%)

    Special strings:
      %id%        - coub ID (identifier in the URL)
      %title%     - coub title
      %creation%  - creation date/time
      %category%  - coub category
      %channel%   - channel title
      %tags%      - all tags (separated by '_')

    Other strings will be interpreted literally.
    This option has no influence on the file extension.
```

Please note that `coub.py` doesn't support `--limit-rate` for now.

## Requirements

#### coub.sh

* Bash >= 4.0
* [jq](https://stedolan.github.io/jq/)
* [FFmpeg](https://www.ffmpeg.org/)
* [curl](https://curl.haxx.se/)
* [grep](https://www.gnu.org/software/grep/)

#### coub.py

* Python 3
* [FFmpeg](https://www.ffmpeg.org/)

## Input

#### Links

The simplest form of input is a direct link to a coub. Only strings that contain `coub.com/view/` will get parsed as coub links.

#### Lists

A list is a simple text file containing one or more coub links. Links must be separated by a white space, tab or new line. Like before only strings that contain `coub.com/view/` will get parsed as coub links.

Example:

```
https://coub.com/view/111111
https://coub.com/view/222222
https://coub.com/view/333333
https://coub.com/view/444444
https://coub.com/view/555555 https://coub.com/view/666666 https://coub.com/view/777777
```

`--write-list` can be used to parse links, lists, channels and tags and output all found coub links into a list for later usage.

#### Channels

Whole channels can be downloaded by providing a full URL or the name of the channel (the name as seen in the URL). By default both original coubs and recoubs will be downloaded. `--no-recoubs` will skip all recoubs, while `--only-recoubs` will only download recoubs.

#### Tags

Tags can be scraped by providing the term or a full URL. Due to a bug (?) in the Coub API you can only download the first 99 pages (i.e. 2475 coubs) listed. All pages afterwards will redirect to page 1.

#### Searches

Coubs from search queries can be downloaded by providing the search term or the corresponding search URL. Please note that searches can (in extreme cases) provide tens of thousands of coub links. The usage of `--limit-num` is advised. 

***

Please note for **channels**, **tags** and **searches** that the URL mustn't include a special sort order (e.g. https://coub.com/tags/tag/likes) or other filters (e.g. https://coub.com/user/reposts). The last word in the URL needs to be the channel name, tag or search term.

***

Input gets parsed in the following order:

* Links  
* Lists  
* Channels  
* Tags
* Searches  

## Changes to the new version

This list documents the (planned) changes since switching from youtube-dl to Coub's API.  

- [x] Download all coubs from a channel
- [x] Download all recoubs from a channel  
- [x] Limit number of downloaded coubs  
- [x] Wait x seconds between downloads  
- [x] Limit download speed  
- [x] Download all coubs with a certain tag  
- [x] Check for the existence of a coub before downloading  
- [x] Specify max. coub duration (FFmpeg syntax) 
- [x] Keep track of already downloaded coubs  
- [x] Export parsed coub links (from channels or tags) to a file for later usage
- [x] Different verbosity levels   
- [x] Choose download order for channels and tags
- [x] Custom output formatting
- [x] Download all coubs from a search query
- [ ] Download stories*  

*Story support will be more difficult to implement, as Coub's API doesn't provide any related endpoint. It will require conventional scraping, after JS execution with a headless browser.
