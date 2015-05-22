# Useful tips, tricks, and plugins for Vim

## Basic Setup
In vim, all of your configuration lives in the `~/.vim` directory. The main file for configuration is
your **vimrc**. This file can be located at either `~/.vimrc` or `~/.vim/vimrc`. The latter is only for
vim versions 7.4+ however it is preferred because then everything is under the same directory.

Under `~/.vim` you can have several directories, the most common of which will be:

* `autoload/` - this is used to define functions and automatically gets loaded when they are called
* `colors/` - this is where colorschemes go
* `ftplugin/` - this will contain files named after filetypes (e.g., `python.vim`) which get loaded when
  a file of that type is opened
* `plugin/` - this is where plugin scripts go
* `syntax/` - this is where syntax files, which define syntax highlighting, go

For more information, see `:help vimfiles`

## Sample vimrc files
* [Ryan's vimrc](https://github.com/ryankuczka/dotfiles/blob/master/vim/vimrc)
* [Ryan's .vim folder](https://github.com/ryankuczka/dotfiles/blob/master/vim)

## Useful Plugins

### Plugin Manager
There are several options. The first you will see a lot of is [pathogen](https://github.com/tpope/vim-pathogen)
which is useful for including plugins but requires manual installation of the plugin files to a directory you
specify. The rest of your options will all be able to download, install, and update plugins for you.

* [Vundle](https://github.com/gmarik/Vundle.vim) was the first, but lacks many features of the others
* [vim-plug](https://github.com/junegunn/vim-plug) is lightweight (only one file) and feature packed.
  This is my preferred manager.
* [neobundle](https://github.com/Shougo/neobundle.vim) is perhaps the most feature packed, but is heavier than
  the others.

### Functional Plugins
These plugins add or extend functionality that seems native to vim.

* [surround.vim](https://github.com/tpope/vim-surround) adds commands to add, change, and delete "surrounding"
  characters like brackets, parenteses, quotes, HTML tags, you name it. Incredibly powerful and very natural.
* [unimpaired.vim](https://github.com/tpope/vim-unimpaired) adds mappings for easy movement between buffers,
  tabs, location lists, quickfix lists, and so much more.
* [commentary.vim](https://github.com/tpope/vim-commentary) adds mappings to easily comment/uncomment code.
* [repeat.vim](https://github.com/tpope/vim-repeat) allows plugin creators to utilize the `.` key for repeating
  the last action. All of [TPope's](https://github.com/tpope) and many other use it.
* [targets.vim](https://github.com/wellle/targets.vim) extends and improves upon vim's native text objects.
* [incsearch.vim](https://github.com/haya14busa/incsearch.vim) extends and improves upon vim's search (`/`)
* [FastFold](https://github.com/Konfekt/FastFold) lets you use slow folding methods like `expr` and `syntax`
  without them being slow!
* [YouCompleteMe](https://github.com/Valloric/YouCompleteMe) is a fast, fuzzy, autocompletion engine which
  supports `<Tab>` completion and more.
* [EasyMotion](https://github.com/Lokaltog/vim-easymotion) adds mappings that make it easy to move the cursor
  anywhere on the screen.

### Command Plugins
These plugins add useful commands (`:`)

* [abolish.vim](https://github.com/tpope/vim-abolish) adds a `:S[ubvert]` command which behaves like `:s[ubstitute]`
  but will match and replace different cases and abbreviations too.
* [eunuch.vim](https://github.com/tpope/vim-eunuch) adds commands for interacting with a Unix-like filesystem, including
  commands like `:Move`, `:Rename`, `:Mkdir`, and so on.
* [commandcaps.vim](https://github.com/takac/vim-commandcaps) adds commands so that if you hold the shift key too long,
  things still work so `:Wq`, `:WQ` all do `:wq`

### Interactive Plugins
These plugins use vim to interact with external programs or other vim constructs more easily.

* [ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim) is a fuzzy file finder that is fast, powerful, and extensible.
    * [ctrlp-cmatcher](https://github.com/JazzCore/ctrlp-cmatcher) is a fast, compiled fuzzy matcher for CtrlP
* [fugitive.vim](https://github.com/tpope/vim-fugitive) is an awsome plugin that lets you interact with git from vim!
* [undotree](https://github.com/mbbill/undotree) visualizes vim's powerful but complex undo history.
* [syntastic](https://github.com/scrooloose/syntastic) lets you run linters and checkers from within vim.
* [NERDTree](https://github.com/scrooloose/nerdtree) lets you interact with the file system.
* [tagbar](https://github.com/majutsushi/tagbar) provides an interactive overview of your code structure using `ctags`.
* [bufexplorer](https://github.com/jlanzarotta/bufexplorer) helps with managing buffers.

### Visual Plugins
These plugins alter the way vim looks.

* [airline](https://github.com/bling/vim-airline) is a lightweight statusline and tabline plugin which supports
  powerline style fonts, themes, and more
* [indentline](https://github.com/Yggdroot/indentLine) displays indent guides.
* [gitgutter](https://github.com/airblade/vim-gitgutter) displays symbols in the sign column to show lines added,
  changed, or deleted from the current git status
* [css-color](https://github.com/ap/vim-css-color) changes the background of css color codes (e.g., `red`, `#FF0000`,
  or `rgb(255, 0, 0)`) to the color it represents
* [MatchTag](https://github.com/gregsexton/MatchTag) highlights the matching HTML or XML tag to the one you're currently on.

#### Colorschemes
These change the colors of your syntax highlighting. Some popular colorschemes are:

* [lucius](https://github.com/jonathanfilip/vim-lucius)
* [solarized](https://github.com/altercation/vim-colors-solarized)
* [jellybeans](https://github.com/nanotech/jellybeans.vim)
* [hybrid](https://github.com/w0ng/vim-hybrid)
* [gotham](https://github.com/whatyouhide/vim-gotham)

### Filetype Specific Plugins
These plugins add or extend functionality for specific types of files.

* [vim-instant-markdown](https://github.com/suan/vim-instant-markdown) provides a live compiled preview of markdown
  files in your browser.

#### Syntax Plugins
These improve the syntax highlighting for specific filetypes.

* [python](https://github.com/hdima/python-syntax)
* [javascript](https://github.com/pangloss/vim-javascript)
* [nginx](https://github.com/evanmiller/nginx-vim-syntax)
* [LESS](https://github.com/groenewege/vim-less)
* [CSS](https://github.com/hail2u/vim-css3-syntax)
* [HTML](https://github.com/othree/html5.vim)
* [javascript libraries](https://github.com/othree/javascript-libraries-syntax.vim)
* [YAML](https://github.com/ingydotnet/yaml-vim)


## Tips and Tricks

Some of my most commonly used tricks that have greatly helped me

### Lesser known keys and useful mappings
`;` and `,` - these are `next` and `previous` for `f`, `F`, `t`, `T`

`inoremap jj <Esc>` - makes `jj` in insert mode escape to normal (I use this constantly and easier than reaching for <Esc>)

`<C-A>` will complete all matches (i.e., `*.html` followed by `<C-A>` will expand to `file1.html file2.html file3.html`)

### Text Objects
I didn't use these at first but they are probably the single most useful thing I've learned. For full documentation, see `:help text-objects`

For most commands which take a motion, such as `d`, `c`, `y`, you can use an object instead of a motion. This will perform the command on the whole object instead of just from the cursor. Text objects start with an `a` for "a"n object or an `i` for "i"nner object. The types of objects are:
* words (`w`)
* WORDs (`W`)
* sentences (`s`)
* paragraphs (`p`)
* `[]` blocks (`[` or `]`)
* `()` blocks aka blocks (`(`, `)`, or `b`)
* `<>` blocks (`<` or `>`)
* tag blocks (`t`)
* `{}` blocks aka Blocks (`{`, `}`, or `B`)
* a quoted string (`'` or `"`).

Inner text objects won't affect the delimiter in the case of blocks or strings and won't include a trailing space. "A"n object will include delimiters and trailing spaces. This if you have the sentence `This sentence has [square brackets]` and the cursor is inside the brackets, `da[` will leave `This sentence has`, and `di[` will leave `This sentence has []`.

My most common uses are the blocks, notably `ci'`, `ciB`, `cib`. All I need to do is have the cursor inside the quotes or block and this will delete everything inside and put me in insert mode inside it. With the TextObjectify plug-in, sometimes I don't even need to be inside the block since it will seek forward to find the next occurence of the block if it is not inside one.

### Registers

Registers allow you to temporarily store pretty much anything! For full documentation see `:help registers`

Registers are accessed by the `"` key. You can read about all the different types of registers in the docs but the key ones are the unnamed register and the named registers. By default, everything vim deletes using `d`, `c`, `x` or yanked with `y` is stored in the unnamed register which is `"`. Thus to access the unnamed register, it is `""`.

Named registers are accessed by letters, thus there are 26 of them. If you need to put something multiple times and will be deleting or yanking or otherwise filling the unnamed register, you can specify the register to store it in. Thus, to store something in the a register, you would do `"ad[motion]` and then to put it `"ap`. Capital letters will append to the register so `"Ad[motion]` will append to the "a" register.

The other register I often use is the `+` register which is the system clipboard.

When in insert mode `<C-R>x` will put from the "x" register so to put in insert mode (or in command mode) from the unnamed register would be `<C-R>"`.


### Macros

Macros allow you to easily repeat commands and chains of commands. For full documentation see `:help q`.

A macro is recorded by `q` followed by a register when in normal mode. To stop recording, press `q` again. This will record everything you do into the register you choose. To replay a macro, you use `@` followed by the register. `@@` will repeat the last used macro.

Since macros are recorded to registers, it is not necessary to record a macro to use them. If you yank the following into the "c" register (`"cyi'`): `'iI wrote a macro!'` then `@c` will write out "I wrote a macro!"
