### VIM Editor
The vi editor is a very powerful tool and has a very extensive built-in manual, which you can activate using the :help command when the program is started (instead of using man or info, which don't contain nearly as much information). We will only discuss the very basics here to get you started.

What makes vi confusing to the beginner is that it can operate in two modes: command mode and insert mode. The editor always starts in command mode. Commands move you through the text, search, replace, mark blocks and perform other editing tasks, and some of them switch the editor to insert mode.

This means that each key has not one, but likely two meanings: it can either represent a command for the editor when in command mode, or a character that you want in a text when in insert mode.

### INSERT MODE
In insert mode you can use it as normal text editor
To swich to insert mode you have some options:
 +  `i:` Insert before the cursor.
 +  `I:` Insert before the first non-blank character of the line.
 +  `a:` Insert after the cursor.
 +  `A:` Insert at the end of the line.
 +  `o:` Begin a new line below the current line and insert.
 +  `O:` Begin a new line above the current and insert.
 +  `gI:` Insert at column 1 of the line.
 +  `gi:` Insert where insert mode was last stopped.
### Normal MODE:
In Normal Mode you have tons of options. Here I will list the most used ones. You can combo these commands like : :wg
#### BASICS
 + `:e` filename	Open filename for edition
 +  `:w`	Save file
 +  `:q`	Exit Vim
 +  `:w!`	Write file and quit
#### SEARCH
+  `/word`	Search word from top to bottom
+  `?word`	Search word from bottom to top
+  `/jo[ha]n`	Search john or joan
+  `/\<` the	Search the, theatre or then
+  `/the\>`	Search the or breathe
+  `/\< the\>`	Search the
+  `/\< ¦.\>`	Search all words of 4 letters
+  `/\/`	Search fred but not alfred or frederick
+  `/fred\|joe`	Search fred or joe
+  `/\<\d\d\d\d\>`	Search exactly 4 digits
+  `/^\n\{3}`	Find 3 empty lines
+  `:bufdo /searchstr/`	Search in all open files
#### REPLACE
+ `:%s/old/new/g`	Replace all occurences of old by new in file
+ `:%s/old/new/gw`	Replace all occurences with confirmation
+ `:2,35s/old/new/g`	Replace all occurences between lines 2 and 35
+ `:5,$s/old/new/g`	Replace all occurences from line 5 to EOF
+ `:%s/^/hello/g`	Replace the begining of each line by hello
+ `:%s/$/Harry/g`	Replace the end of each line by Harry
+ `:%s/onward/forward/gi`	Replace onward by forward, case unsensitive
+ `:%s/ *$//g`	Delete all white spaces
+ `:g/string/d`	Delete all lines containing string
+ `:v/string/d`	Delete all lines containing which didn’t contain string
+ `:s/Bill/Steve/`	Replace the first occurence of Bill by Steve in current line
+ `:s/Bill/Steve/g`	Replace Bill by Steve in current line
+ `:%s/Bill/Steve/g`	Replace Bill by Steve in all the file
+ `:%s/\r//g`	Delete DOS carriage returns (^M)
+ `:%s/\r/\r/g`	Transform DOS carriage returns in returns
+ `:%s#<[^>]\+>##g`	Delete HTML tags but keeps text
+ `:%s/^\(.*\)\n\1$/\1/`	Delete lines which appears twice
+ `Ctrl+a`	Increment number under the cursor
+ `Ctrl+x`	Decrement number under cursor
+ `ggVGg?`	Change text to Rot13
#### CASE
+ `Vu`	Lowercase line
+ `VU`	Uppercase line
+ `g~~`	Invert case
+ `vEU`	Switch word to uppercase
+ `vE~`	Modify word case
+ `ggguG`	Set all text to lowercase
+ `:set ignorecase`	Ignore case in searches
+ `:set smartcase`	Ignore case in searches excepted if an uppercase letter is used
+ `:%s/\<./\u&/g`	Sets first letter of each word to uppercase
+ `:%s/\<./\l&/g`	Sets first letter of each word to lowercase
+ `:%s/.*/\u&`	Sets first letter of each line to uppercase
+ `:%s/.*/\l&`	Sets first letter of each line to lowercase
#### READ/WRITE FILES
+ `:1,10 w outfile`	Saves lines 1 to 10 in outfile
+ `:1,10 w >> outfile`	Appends lines 1 to 10 to outfile
+ `:r infile`	Insert the content of infile
+ `:23r infile`	Insert the content of infile under line 23
#### FILE EXPLORER
+ `:e` .	Open integrated file explorer
+ `:Sex`	Split window and open integrated file explorer
+ `:browse e`	Graphical file explorer
+ `:ls`	List buffers
+ `:cd ..`	Move to parent directory
+ `:args`	List files
+ `:args *.php`	Open file list
+ `:grep expression *.php`	Returns a list of .php files contening expression
+ `gf`	Open file name under cursor
