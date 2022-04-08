Vim-cellmode
============
This a vim plugin that enables MATLAB-style cell mode execution for python
scripts in vim, assuming an ipython interpreter running in screen (or tmux).

Demo
----
[![Youtube demo video](http://img.youtube.com/vi/ju50L7Fcn7w/0.jpg)](http://www.youtube.com/watch?v=ju50L7Fcn7w)

Usage
-----

Blocks are delimited by `##`, `#%%` or `# %%` (customizable through
`cellmode_cell_delimiter`).
For example, say you have the following python script :

    ##
    import numpy as np
    print 'Hello'                  # (1)
    np.zeros(3)
    ##
    if True:
      print 'Yay !'                # (2)
      print 'Foo'                  # (3)
    ##

If you put your cursor on the line marked with (1) and hit Ctrl-G, the 3 lines
in the first cell will be sent to tmux. If you hit Ctrl-B, the same will happen
but the cursor will move to the line after the ## (so you can chain Ctrl-B).

You can also select line(s) and hit Ctrl-C to send them to tmux. The plugin
automatically deindent selected lines so that the first line has no
indentation. So for example if you select the line marked (2) and (3), the
print statements will be de-indented and sent to tmux and ipython will
correctly run them.

Requirements
------------
On Linux, you need xclip if you want to use screen.

Keys mapping
-----------
By default, the following mappings are enabled :

* *C-c* sends the currently selected lines to tmux
* *C-g* sends the current cell to tmux
* *C-b* sends the current cell to tmux, moving to the next one

You can disable default mappings :

    let g:cellmode_default_mappings='0'

In addition, there is a function to execute all cells above the current line
which isn't bound by default, but you can easily bind it with :

    noremap <silent> <C-a> :call RunTmuxPythonAllCellsAbove()<CR>

Required configurations
-----------------------
**vim-cellmode** needs the following information before it works properly:
1. Where to store the temporary files.
2. Wich platform is being used: TMUX or Screen?
3. Which TMUX pane runs ipython (if TMUX is used) 

### Temporary files
The plugin uses a temporary file as an in-between for sending text from Vim to TMUX. Specifically, to
allow cell execution queuing, we use a rolling buffer of temporary files. 

Therefore Vim needs access to a folder where temporary files can be placed. A simple way to solve this is: 
```
# Making a tmp directory
mkdir $HOME/tmp
# Set the TMPDIR environment variable and launch vim
TMPDIR=$HOME/tmp vim
```

**COMMON SIGN OF ERROR:** You will likely see gibberish pasted into your vim buffer if this step is incomplete!

### Choosing between TMUX or Screen
To choose between tmux and screen, set `g:cellmode_use_tmux=1` (or 0 if you want screen).
Note that currently, CopyToScreen relies on OSX' pbcopy to set the paste buffer.

### ipython pane number 

The plugin needs to know the TMUX pane number that ipython runs in. E.g. if you have two panes with Vim on top and ipython below, then Vim runs in pane 0 and ipython in pane 1. You must then specify that that ipython runs in pane 1. You do this with:
```
let g:cellmode_tmux_panenumber='1'
```
Note that the default value is 0. However, this differs form where most people place their consoles.

Not sure about the pane numbers? Try the default TMUX command `prefix + q` to see the correct numbers.



Other Options
-------------
You have to configure the target tmux/screen session/window/pane. By default, the
following is used :

    let g:cellmode_tmux_sessionname=''  " Will try to automatically pickup tmux session
    let g:cellmode_tmux_windowname=''
    let g:cellmode_tmux_panenumber='0'

    let g:cellmode_screen_sessionname='ipython'
    let g:cellmode_screen_window='0'


You can control the size of the buffer by defining `g:cellmode_n_files` (10
by default).

You can also configure the cell delimiter. This is done through the
`g:cellmode_cell_delimiter_variable` (prefix it with b: to only
affect the current buffer). This is used inside a regexp so you can use regexp
in it. So for example

    set g:cellmode_cell_delimiter='\(##\|#%%\|#\s%%\)'

will match `##`, `#%%` and `# %%` as cell delimiters. This is the default configuration.

Difference with vim-ipython
---------------------------
Note that if you want more advanced integration with IPython (using the new
multi-client architecture), there is the vim-ipython project :
https://github.com/ivanov/vim-ipython/

The main difference with vim-ipython is that this plugin simply emulate a paste
as you would do it manually from vim to ipython. This allow to see the result
of the execution directly in the ipython split whereas vim-ipython uses a
separate vim buffer to show the results.

License
-------
MIT (see LICENSE)
