# TTS Reader

A preprocessing script for converting text to speech.

Helps convert news articles into `.flac` files that you can listen to at your
own
convenience.

The idea behind this tool is to have a TXT file where the user would put web
pages in read mode and other text-based information separated by `...` or other
separation string you specify.

The script will read the data from the file, remove redundancy, adapt it for
TTS and covert it to `.mp3`

Previous implementations of the TTS Reader:

- [python_tts](https://github.com/maxxxxxdlp/python_tts/)
- [TTS King](https://github.com/maxxxxxdlp/tts_king/)

## Installation

Install dependencies:

```sh
npm install
```

## Workflow

1. Create a big text file (articles from the web, a book, a document, etc)
2. Run this script to preprocess the file (see details on the processing done
   below)
3. Run a text-to-speech software on the final file (i.e. macOS's `say`)

Example usage with macOS's `say`:

```sh
# Generate an out.txt, out_2.txt, ... files based on processed input
npx ts-node-esm src/run.ts --input ./in.txt --output ./out.txt
# Go though each text file that hasn't yet been converted
for f in out*.txt; do
    echo "Generating $f.flac";
    # convert to audio
    say -r 250 -o "$f.flac" --progress "`cat $f`";
    # and delete the source text file
    rm "$f";
done 
```

To see all available options, run the main script with `--help` argument:

```
npx ts-node-esm src/run.ts --help
```

To see available `say` options, run `man say`.

To see supported `say` file formats, run `say --file-format=\? --data-format=\?`

## Preprocessing steps

1. Strip HTML
2. Strip Emoji
3. Strip block listed lines (defined in [./src/config.ts](./src/config.ts)).
   Those currently consist of the spam lines of text I found commonly repeated
   in the websites I often visit. (i.e `Advertisement`,
   `RECOMMENDED VIDEOS FOR YOU`, and other trash)
4. Stip non-text lines (defined as lines that have more than 30% of
   non-English-letter characters). This strips out code, spam, math equations
   and other things that are not friendly with text-to-speech software.
5. Remove repeated lines. This is perhaps the most important one. It can
   dramatically reduce the size of the input file (by about 30%-50%)

   Why this is super cool:

    - If you accidentally pasted the same news article twice, this step will
      remove the duplicate
    - It will automatically remove all the commonly repeated lines like
      `Advertisement`, or footers from websites (i.e, Wired has a whole bunch of
      lines like`More Great WIRED Stories` at the end of each article)

## Finding Voices

There is a helper script provided for finding the best voice among those
available
on your system. Note, this only works with macOS's `say` command.

To see available options, run it with `--help` argument:

```
npx ts-node-esm src/findVoice.ts --help
```

Example call:

```sh
npx ts-node-esm src/findVoice.ts --voices Ava Karen Samantha --speed 200 --text "Hi! Isn't it cool to have a computer talk to you?"
```

By default, `say` will use the voice you configured in your macOS settings.
[Documentation](https://support.apple.com/guide/mac-help/change-spoken-content-settings-accessibility-spch638/mac)