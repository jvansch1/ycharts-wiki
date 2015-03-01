# Useful tips, tricks, and plugins for Vim

## Sample vimrc files
[Ryan's vimrc](https://gist.github.com/ryankuczka/5664030#file-vimrc)

[Daniel's vimrc](https://gist.github.com/ryankuczka/74871d17f0426ac4cea2#file-daniel_vimrc-vim)

[Farhan's vimrc](https://gist.github.com/ryankuczka/ddb274105c3f48dde839#file-farhan_vimrc-vim)

## Useful Plugins

**[Vundle](https://github.com/gmarik/vundle)**

Vundle is a Vim bundle plugin manager and makes it ridiculously easy to find and maintain vim plugins

**[fugitive.vim](https://github.com/tpope/vim-fugitive)**

A Vim Git wrapper so awesome it should be illegal. `Bundle 'fugitive.vim'`

**[surround.vim](https://github.com/tpope/vim-surround)**

Provides easy ways to change surrounding characters such as brackets or quotes. `Bundle 'surround.vim'`

**[abolish.vim](https://github.com/tpope/vim-abolish)**

Enhances search, substitutions, and abbreviations. `Bundle 'abolish.vim'`

**[unimpaired.vim](https://github.com/tpope/vim-unimpaired)**

Adds a ton of keyboard mappings to better navigate buffers, tabs, add empty lines, etc. `Bundle 'unimpaired.vim'`

**[NERDCommenter](http://www.vim.org/scripts/script.php?script_id=1218)**

NERDCommenter adds normal key mappings to quickly comment/uncomment blocks of code `Bundle 'The-NERD-Commenter'`

**[AutoClose](http://www.vim.org/scripts/script.php?script_id=1849)**

Autoclose brackets as you type. `Bundle 'AutoClose'`

**[Rename2](http://www.vim.org/scripts/script.php?script_id=2724)**

Rename the file you are currently editing and keep on editing `Bundle 'Rename2'`

**[indentLine.vim](http://www.vim.org/scripts/script.php?script_id=4354)**

Adds a vertical line to show indent levels. `Bundle 'Yggdroot/indentLine'`

**[TextObjectify](https://github.com/paradigm/TextObjectify)**

Changes some of the unexpected ways text objects work to how they should be expected. `Bundle 'paradigm/TextObjectify'`

**[CSS Color](https://github.com/skammer/vim-css-color)**

Highlights the background of a color string as that color `Bundle 'skammer/vim-css-color'`

**[Git Gutter](https://github.com/airblade/vim-gitgutter)**

Adds plus, minus, changed signs to the gutter based on the current git diff. `Bundle 'airblade/vim-gitgutter'`

**[Ack](http://www.vim.org/scripts/script.php?script_id=2572)**

Provides a slick interface to ack with interactive search results that take you directly to the matched line in a file `Bundle 'ack.vim'`

**[Syntastic](https://github.com/scrooloose/syntastic)**

Syntax checking/linting - lets you run jshint or flake8 with a single click and conveniently takes you to your errors - highly recommended `Bundle 'Syntastic'`

**[CtrlP](http://www.vim.org/scripts/script.php?script_id=3736) ([more info](http://kien.github.com/ctrlp.vim/))**

Lets you quickly search for files with powerful fuzzy-matching autocompletion. `Bundle 'ctrlp.vim'`

**[NERD Tree](http://www.vim.org/scripts/script.php?script_id=1658)**

Tree explorer for navigating the file system `Bundle 'The-NERD-tree'`

**[TAGList](http://www.vim.org/scripts/script.php?script_id=273)**

Code browser (generates tags from variables, functions, classes that allow for easy jumping through code) `Bundle 'taglist.vim'`

**[ShowTrailingWhitespace](http://www.vim.org/scripts/script.php?script_id=3966)**

Highlights all trailing white space. `Bundle 'ShowTrailingWhitespace'`

**[DeleteTrailingWhitespace](http://www.vim.org/scripts/script.php?script_id=3967)**

Sister plugin to the above - lets you delete all trailing white space with 1 command `Bundle 'DeleteTrailingWhitespace'`

**[Startify](https://github.com/mhinz/vim-startify)**

Provides a nice start screen with recent and bookmarked files `Bundle 'mhinz/vim-startify'`

**[SnipMate](http://www.vim.org/scripts/script.php?script_id=2540)**

TextMate-style snippets for Vim `Bundle 'snipMate'`

**[mru.vim](http://www.vim.org/scripts/script.php?script_id=521)**

Plugin to manage most recently used files `Bundle 'mru.vim'`

**[Keep Window on Buffer Delete](http://www.vim.org/scripts/script.php?script_id=2103)**

Lets you quickly and easily delete the current buffer without killing the window - just type \bd - very useful!

## Pleasant color-schemes

**[Lucius](http://www.vim.org/scripts/script.php?script_id=2536)**

**[Molokai](http://www.vim.org/scripts/script.php?script_id=2340)**

**[Pyte](http://www.vim.org/scripts/script.php?script_id=1492)**

**[Sahara](http://blog.sanctum.geek.nz/sahara-vim-colorscheme/)**

**[Zenburn](http://www.vim.org/scripts/script.php?script_id=415)**

**[Vim Colorschemes](https://github.com/flazz/vim-colorschemes)** -- Large repo of vim colorschemes to choose from `Bundle 'flazz/vim-colorschemes'`


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
