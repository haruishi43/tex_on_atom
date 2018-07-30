# LaTeX Environment on my MacOS

## Environment Setup

Based on [this article](https://qiita.com/ken0nek/items/f98f88c9c45d8499786e).

### Downloads:

- [MacTex](http://www.tug.org/mactex/)
  - LaTeX engine
- [atom](https://qiita.com/ken0nek/items/f98f88c9c45d8499786e)
  - Editor
- [skim-app](https://skim-app.sourceforge.io/)
  - Viewer

#### Atom

Setup packages:

```bash
apm install language-latex latexer
```

These packages are used for code completion.

#### Skim

- \[Preferences\] -> \[Sync\]
  - change `preset` to atom
  - check `check for file changes`

```bash
defaults write -app Skim SKAutoReloadFileUpdate -boolean true
```

### Setting up LaTeX engine:

`uplatex` or `platex` are LaTeX engine used for Japanese compilation.

```bash
sudo tlmgr update --self --all
sudo tlmgr install uplatex latexmk collection-langjapanese
```

#### Font setup

```bash
cd /usr/local/texlive/2016basic/texmf-dist/scripts/cjk-gs-integrate
sudo perl cjk-gs-integrate.pl --link-texmf --force
sudo mktexlsr
sudo kanji-config-updmap-sys hiragino-elcapitan-pron
```

### `latexmk`

`latexmk` is `make` command for `latex`.
Using `.latexmkrc`, you can create `pdf` files from `tex` automatically.


```latexmkrc
#!/usr/bin/env perl

$latex                       = 'uplatex -synctex=1 -halt-on-error %O %S';
$bibtex                      = 'upbibtex %O %B';
$dvipdf                      = 'dvipdfmx %O -o %D %S';
$makeindex                   = 'mendex -U %O -o %D %S';
$max_repeat                  = 5;
$pdf_mode                    = 3; # generates pdf via dvipdfmx

# Prevent latexmk from removing PDF after typeset.
# This enables Skim to chase the update in PDF automatically.
$pvc_view_file_via_temporary = 0;

# Use Skim as a previewer
$pdf_previewer               = 'open -a /Applications/Skim.app'; # Open using Skim.app
```

## Commands:

Put `.latexmkrc` in the working directory.

Automatic build after save command in atom:

```bash
latexmk -pvc sample.tex
```

## Makefile:

```Makefile
INPUT=thesis.tex

OUTPUT=thesis

// VIEWER=open -a /Applications/Skim.app

all:
    latexmk -jobname=$(OUTPUT) $(INPUT)

clean:
    latexmk -CA -jobname=$(OUTPUT) $(INPUT)
    find . -name *.aux -delete
    find . -name *.log -delete
    rm *.bbl

view:
    latexmk -pv -jobname=$(OUTPUT) $(INPUT)

watch:
    latexmk -pvc -jobname=$(OUTPUT) $(INPUT)
```
