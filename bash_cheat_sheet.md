### Bash Cheat Sheet
Everything between < > need to be changed to some string (including the <>)
 To create a bash file:
 - ```cd <dir>``` -> The directory to push the bash file
 - ```touch <file>.sh``` -> to create files use the touch command
 - ```vim <file>.sh``` -> edit the file you need  (u can use other editors as well like nano or gedit)
 - Make sure u always start your bash script with #!/bin/bash

To execute the bash file:
 - ``` chmod 775 <file>.sh ``` -> first make it executeable
 - ```./<file>.sh``` -> make sure in the right dir
#### IO Redirection
```cmd < file``` Input of cmd from file
```cmd1 <(cmd2)``` Output of cmd2 as file input to cmd1
```cmd > file``` Standard output (stdout) of cmd to file
```cmd > /dev/null``` Discard stdout of cmd
```cmd >> file``` Append stdout to file
```cmd 2> file``` Error output (stderr) of cmd to file
```cmd 1>&2``` stdout to same place as stderr
```cmd 2>&1``` stderr to same place as stdout
```cmd &> file``` Every output of cmd to file
cmd refers to a command.

```eval``` can run a command that sits in variable
```
commandvariable=”ls -l /home | sort”
eval $commandvariable
```
##### Example
```
#!/bin/bash
eval $* ## executes the command given as parameters
```

#### Compressing / Uncompressing
Compress an Entire Directory or a Single File
```
tar -czvf name-of-archive.tar.gz /path/to/directory-or-file
```

Exctracting a tar.gz file
```
tar -xzvf file.tar.gz
```
Here’s what those switches actually mean:

-c: Create an archive.
-x: Extract an archive.
-z: Compress the archive with gzip.
-v: Display progress in the terminal while creating the archive, also known as “verbose” mode. The v is always optional in these commands, but it’s helpful.
-f: Allows you to specify the filename of the archive.

Exclude Directories and Files
In some cases, you may wish to compress an entire directory, but not include certain files and directories. You can do so by appending an --exclude switch for each directory or file you want to exclude.

For example, let’s say you want to compress /home/ubuntu, but you don’t want to compress the /home/ubuntu/Downloads and /home/ubuntu/.cache directories. Here’s how you’d do it:
```
tar -czvf archive.tar.gz /home/ubuntu --exclude=/home/ubuntu/Downloads --exclude=/home/ubuntu/.cache
```
The --exclude switch is very powerful. It doesn’t take names of directories and files–it actually accepts patterns. There’s a lot more you can do with it. For example, you could archive an entire directory and exclude all .mp4 files with the following command:
```
tar -czvf archive.tar.gz /home/ubuntu --exclude=*.mp4
```






#### Variables
NOTES: 
 - When u define a variable like -> VAR1="kacsa" do not use spaces before and after the = sign.
 - Using variable like $(pwd) -> what it does is that it will catch the return of that command and set it as the variables data
 - You can use variables in echo between "" and it is a good practice to put your variable between {} like so : "my name is :${name}!" -> this will make your code easier to read. If you use echo with '' you are not able to use variables inside.  

#### Some Useful Bash reserved variables  
 - GROUPS <- An array variable containing the list of groups of which the current user is a member.
 - LANG <- Used to determine the locale category for any category not specifically selected with a variable starting with LC_.
 - HOME <- The current user's home directory; the default for the cd built-in. The value of this variable is also used by tilde expansion.
 - PATH <- A colon-separated list of directories in which the shell looks for commands.
 - CDPATH <- A colon-separated list of directories used as a search path for the cd built-in command.
 - RANDOM <- Each time this parameter is referenced, a random integer between 0 and 32767 is generated. Assigning a value to this variable seeds the random number generator.
 - PWD <- The current working directory as set by the cd built-in command.
 - env <- Show environment variables
 
Echo a command as variable
```
echo "The value of the pwd command is $(pwd)"
NOTE : IF you just using $pwd it will echo nothing as pwd!
```
Assign command output to a variable
```
output=$(pwd)
echo "The value of the output variable is ${output}"
```
#### Input from command line
You can pass input parameters to the script by passing parameters after the script excecution
```
./<file>.sh kicsi macska 
```
In the bash script you can get these parameters using the following commands:
- $0 <- Name of this shell script itself. 
- $1 <- Value of first command line parameter (similarly $2, $3, etc) 
- $# <- In a shell script, the number of command line parameters. 
- $* OR $@ <- All of the command line parameters. 
- $- <- Options given to the shell. 
- $? <- Return the exit status of the last command. 
- $$ <- Process id of script (really id of the shell running the script)

Read data from the user
```
echo "Enter a value: "
read userInput
echo "You just entered $userInput"
```
#### Math Calculation

Integer Math

First way to do math with integer (and only integer) is to use the command “expr — evaluate expression”.
```
myvar=$(expr 1 + 1)
echo $myvar
2
```
Another alternative to expr, is to use the bash builtin command let.
```
let myvar+=1
echo $myvar
3
```
Also, you can simply use the parentheses or square brackets :
```
echo $((myvar+2))
5
echo $[myvar+2]
5
myvar=$((myvar+3))
```

#### If Statement

Example:
``` 
if[-s file]
then
    #such and such
fi
```
Checking strings:
 - ```s1 = s2```  <- Check if s1 equals s2. 
 - ```s1 != s2``` <- Check if s1 is not equal to s2. 
 - ```-z s1```    <- Check if s1 has size 0. 
 - ```-n s1```    <- Check if s2 has nonzero size. 
 - ```s1```       <- Check if s1 is not the empty string.

Example:
```
if[$myvar = "hello"] ; then
    echo"We have a match"
fi
```
Checking numbers: 
Note that a shell variable could contain a string that represents a number. If you want to check the numerical value use one of the following:
 - ```n1 -eq n2``` <- Check to see if n1 equals n2. 
 - ```n1 -ne n2``` <- Check to see if n1 is not equal to n2. 
 - ```n1 -lt n2``` <- Check to see if n1 < n2. 
 - ```n1 -le n2``` <- Check to see if n1 <= n2. 
 - ```n1 -gt n2``` <- Check to see if n1 > n2. 
 - ```n1 -ge n2``` <- Check to see if n1 >= n2.

Example:
```
if[$# -gt 1 ] 
then
    echo"ERROR: should have 0 or 1 command-line parameters"
fi
```
Boolean operators:
 - ```!```  <- not 
 - ```-a``` <- and 
 - ```-o``` <- or
Example:
```
if[$num -lt 10 -o $num -gt 100 ]
then
    echo"Number $num is out of range"
elif[! -w $filename]
then
    echo"Cannot write to $filename"
fi
```

NOTE: Most UNIX commands return a true (nonzero) or false (0) in the shell variable status to indicate whether they succeeded or not. This return value can be checked. At the command line echo $status. In a shell script use something like this:
```
if grep-q shell bshellref 
then
    echo"true"
else
    echo"false"
fi
```
Note that -q is the quiet version of grep. It just checks whether it is true that the string shell occurs in the file bshellref. It does not print the matching lines like grep would otherwise do

#### Expressions used with if
The ones below contains an overview of the so-called "primaries" that make up the TEST-COMMAND command or list of commands. These primaries are put between square brackets to indicate the test of a conditional expression.
 - [ -a <FILE> ] <- True if <FILE> exists.
 - [ -b <FILE> ] <- True if FILE exists and is a block-special file.
 - [ -c <FILE> ] <- True if FILE exists and is a character-special file.
 - [ -d <FILE> ] <- True if FILE exists and is a directory..
 - [ -e <FILE> ] <- True if FILE exists.
 - [ -f <FILE> ] <- True if FILE exists and is a regular file.
 - [ -g <FILE> ] <- True if FILE exists and its SGID bit is set.
 - [ -h <FILE> ] <- True if FILE exists and is a symbolic link.
 - [ -k <FILE> ] <- True if FILE exists and its sticky bit is set.
 - [ -p <FILE> ] <- True if FILE exists and is a named pipe (FIFO).
 - [ -r <FILE> ] <- True if FILE exists and is readable.
 - [ -s <FILE> ] <- True if FILE exists and has a size greater than zero.
 - [ -w <FILE> ] <- True if FILE exists and is writable.
 - [ -x <FILE> ] <- True if FILE exists and is executable.
 - [ -O <FILE> ] <- True if FILE exists and is owned by the effective user ID.
 - [ -G <FILE> ] <- True if FILE exists and is owned by the effective group ID.
 - [ -L <FILE> ] <- True if FILE exists and is a symbolic link.
 - [ -N <FILE> ] <- rue if FILE exists and has been modified since it was last read.
 - [ <FILE1> -nt <FILE2> ] <- True if FILE1 has been changed more recently than FILE2, or if FILE1 exists and FILE2 does not.
 - [ <FILE1> -ot <FILE2> ] <- True if FILE1 is older than FILE2, or is FILE2 exists and FILE1 does not.
 - [ <FILE1> -ef <FILE2> ] <- True if FILE1 and FILE2 refer to the same device and inode numbers.
 - [ -z <STRING> ] <- True of the length if "STRING" is zero.
 - [ -n <STRING> ] or [ <STRING> ] <- True if the length of "STRING" is non-zero.
 - [ <STRING1> == <STRING2> ] <- True if the strings are equal. "=" may be used instead of "==" for strict POSIX compliance.
 - [ <STRING1> != <STRING2> ] <- True if the strings are not equal.
 - [ <STRING1> < <STRING2> ] <- True if "STRING1" sorts before "STRING2" lexicographically in the current locale.
 - [ <STRING1> > <STRING2> ] <- True if "STRING1" sorts after "STRING2" lexicographically in the current locale.

##### Pattern Matching:
 - ```*``` <- Matches 0 or more characters. 
 - ```?``` <- Matches 1 character. 
 - ```[AaBbCc]``` <- Example: matches any 1 char from the list. 
 - ```[^RGB]```  <- Example: matches any 1 char not in the list. 
 - ```[a-g] ```<- Example: matches any 1 char from this range.
##### Quoting:
 - ```\c``` <- Take character c literally. 
 - ````cmd```` <- Run cmd and replace it in the line of code with its output. 
 - ```"whatever"```  <- Take whatever literally, after first interpreting $, `...`, \ 
 - ```'whatever'```  <- Take whatever absolutely literally.
```
match=`ls*.bak` #Puts names of .bak files into shell variable match.
echo \* #Echos * to screen, not all filename as in:  
echo *echo'$1$2hello' #Writes literally $1$2hello on screen.
echo"$1$2hello" #Writes value of parameters 1 and 2 and string hello
```
#### Parameter Expansions and Strings
``` 
#!/bin/bash
rand_str="A random string"
# Get string length
echo "String Length : ${#rand_str}"
# Get string slice starting at index (0 index)
echo "${rand_str:2}"
# Get string with starting and ending index
echo "${rand_str:2:7}"
# Return whats left after A
echo "${rand_str#*A }"
```
##### Case statement: 
Here is an example that looks for a match with one of the characters a, b, c. If $1 fails to match these, it always matches the * case. A case statement can also use more advanced pattern matching.
```
case "$1" in    
    a) cmd1 ;;     
    b) cmd2 ;;    
    c) cmd3 ;; 
    *) cmd4 ;; 
esac
```
##### Loops:
Bash supports loops written in a number of forms,
```
for arg in [list] 
do     
    echo $arg 
done

for arg in [list] ; do     
    echo $arg 
done
```
You can supply [list] directly
```
NUMBERS="1 2 3" 
for number iN `echo $NUMBERS`
do
    echo $number
done

for number in $NUMBERS
do
    echo -n $number
done

for number in 1 2 3 
do
    echo -n $number 
done
```
If [list] is a glob pattern then bash can expand it directly, for example:
```
for file in *.tar.gz 
do
    tar-xzf $file 
done
```
You can also execute statements for [list], for example:
```
for x in `ls-tr*.log`
do
    cat $x &gt;&gt; biglog 
done
```
#### Arrays
Bash arrays can only have one dimension and indexes start at 0
Messing with arrays
```
#!/bin/bash
	
# Create an array
fav_nums=(3.14 2.718 .57721 4.6692)

echo "Pi : ${fav_nums[0]}"

# Add value to array
fav_nums[4]=1.618

echo "GR : ${fav_nums[4]}"

# Add group of values to array
fav_nums+=(1 7)

# Output all array values
for i in ${fav_nums[*]}; do
	echo $i;
done

# Output indexes
for i in ${!fav_nums[@]}; do
	echo $i;
done

# Get number of items in array
echo "Array Length : ${#fav_nums[@]}"

# Get length of array element
echo "Index 3 length : ${#fav_nums[3]}"

# Sort an array
sorted_nums=($(for i in "${fav_nums[@]}"; do
	echo $i;
done | sort))

for i in ${sorted_nums[*]}; do
	echo $i;
done

# Delete array element
unset 'sorted_nums[1]'

# Delete Array
unset sorted_nums
```

##### Arithmetic:
With bash, an expression is normally enclosed using [] and can use the following operators, in order of precedence:
 - \* / % <- (times, divide, remainder) 
 - \+ -  <- (add, subtract) 
 - < > <= >=  <- (the obvious comparison operators) 
 - == != <-(equal to, not equal to) 
 - &&  <-  (logical and) 
 - ||  <-  (logical or) 
 - =  <- (assignment)

Arithmetic is done using long integers. Example:
```
result=$[$1 + 3]  # In this example we take the value of the first parameter, add 3, and place the sum into result.
```
##### Other Shell Features:
 - ```$var```         <-Value of shell variable var. 
 - ```${var}abc```    <-Example: value of shell variable var with string abc appended. 
 - ```# ```           <- At start of line, indicates a comment. 
 - ```var=value```    <- Assign the string value to shell variable var. 
 - ```cmd1 && cmd2``` <- Run cmd1, then if cmd1 successful run cmd2, otherwise skip. 
 - ```cmd1 || cmd2``` <- Run cmd1, then if cmd1 not successful run cmd2, otherwise skip. 
 - ```cmd1; cmd2```   <- Do cmd1 and then cmd2. 
 - ```cmd1 & cmd2```  <- Do cmd1, start cmd2 without waiting for cmd1 to finish. 
 - ```(cmds) ```      <- Run cmds (commands) in a subshell.
Using the | (pipe) operator the output of a command can be redirected to be the input of a second command
```ls --help | less```
```ls --help | more```
```ls -l /etc | grep net```

### Functions
You can use functions to avoid the need to write duplicate code
```
#!/bin/bash
 # Define function
 getDate() {
 	
 	# Get current date and time
 	date
 	
 	# Return returns an exit status number between 0 - 255
 	return
 }
 
 getDate
 
 # This is a global variable
 name="Derek"
 
 # Local variable values aren't available outside of the function
 demLocal() {
 	local name="Paul"
 	return
 }
 
 demLocal
 
 echo "$name"
 
 # A function that receives 2 values and prints a sum
 getSum() {
 
 	# Attributes are retrieved by referring to $1, $2, etc.
 	local num3=$1
 	local num4=$2
 	
 	# Sum values
 	local sum=$((num3+num4))
 	
 	# Pass values back with echo
 	echo $sum
 }
 
 num1=5
 num2=6
 
 # You pass atributes by separating them with a space
 # Surround function call with $() to get the return value
 sum=$(getSum num1 num2)
 echo "The sum is $sum"
```


### TERMINAL COMMANDS 

#### Search Files
```grep pattern files``` Search for pattern in files
```grep -i``` Case insensitive search
```grep -r``` Recursive search
```grep -v``` Inverted search
```grep -o``` Show matched part of file only
```find /dir/ -name name*``` Find files starting with name in dir
```find /dir/ -user name``` Find files owned by name in dir
```find /dir/ -mmin num``` Find files modifed less than num minutes ago in dir
```whereis command``` Find binary / source / manual for command
```locate file``` Find file (quick search of system index)

### File Operations
```touch file1```Create file1
```cat file1 file2```Concatenate files and output
```less file1```View and paginate file1
```file file1```Get type of file1
```cp file1 file2```Copy file1 to file2
```mv file1 file2```Move file1 to file2
```rm file1```Delete file1
```head file1```Show first 10 lines of file1
```tail file1```Show last 10 lines of file1
```tail -F file1```Output last lines of file1 as it changes
### File Permissions
```chmod 775 file``` Change mode of file to 775
```chmod -R 600 folder``` Recurs­ively chmod folder to 600
```chown user:group file``` Change file owner to user and group to group
### File Permission Numbers
First digit is owner permis­sion, second is group and third is everyone.
Calculate permission digits by adding numbers below.
```
4  read (r)
2  write (w)
1  execute (x)
```
```
ls -l output

-					- the file tpye 
	rwx 			- permission for the owner
		rwx 		- permission for the group
			r-x		- permission for everybony else

chmod ugo+w file1
		 -
		 =
		 
chmod g+x file1 // add executable permission to group
```


```du``` - estimate file space usage

to list files and directories with their sizes sorted use:
```
du -h --max-depth=1 /home/paprika/ | sort -hr
```

#### File structure
![files](http://dev-random.net/wp-content/uploads/2013/10/linux-directory-structure.jpg "files")  

 - /etc/group contains the groups
 - /etc/passwd contains the users
 - /etc/shadow contains the passwords

#### Hard Links
Both Linux / UNIX allows the data of a file to have more than one name in separate places in the same file system. Such a file with more than one name for the same data is called a hard-linked file. How do I create a hard link under Linux / UNIX / Apple Mac OS X / BSD operating systems?

A hard link to a file is indistinguishable from the original directory entry; any changes to a file are effectively independent of the name used to reference the file. Hard links may not normally refer to directories and may not span file systems.

```ln``` command Example To Make a Hard Link 
The ln command make links between files. By default, ln makes hard links. The syntax is as follows for Unix / Linux hard link command: 
ln {source} {link}
Where, source is an existing file. link is the file to create (a hard link).
To create hard link for foo file, enter:
```
echo 'This is a test' > foo
ln foo bar
ls -li bar foo
```
Sample outputs:
```
4063240 -rw-r--r-- 2 root root 15 Oct  1 15:30 bar
4063240 -rw-r--r-- 2 root root 15 Oct  1 15:30 foo
```
Problem with hard links: works only on the same filesystem

#### Soft/Symbolic link
Symbolic links are like shortcuts or references to the actual file or directory. Most of the time these links are transparent when working with them through other programs. For example you could have a link to a directory to one hard drive inside a directory that lies on a different hard drive and an application will still treat it the same.

Symbolic links are used all the time to link libraries and make sure files are in consistent places without moving or copying the original. Links are often used to “store” multiple copies of the same file in different places but still reference to one file.
To create a symbolic link in Linux we use this syntax:
```
ln -s /path/to/original/ /path/to/linkName
```
What happens if I edit the link? Any modifications to the linked file will be changed on the original file. What happens if I delete the link? If you delete the link the original file is unchanged. It will still exist. What happens if I delete the original file but not the link? The link will remain but will point to a file that does not exist. This is called an orphaned or dangling link.

#### The Difference Between Soft and Hard Links
##### Hard links
 - Only link to a file not a directory
 - Can not reference a file on a different disk/volume
 - Links will reference a file even if it is moved
 - Links reference inode/physical locations on the disk
##### Symbolic (soft) links
 - Can link to directories
 - Can reference a file/folder on a different hard disk/volume
 - Links remain if the original file is deleted
 - Links will NOT reference the file anymore if it is moved
 - Links reference abstract filenames/directories and NOT physical locations. They are given their own inode

### Practice Makes Perfect
To really grasp the concept of symbolic links lets give it a shot.

Go into the tmp directory:
```
cd /tmp
```
Make a directory
```
mkdir original
```
Copy over the host.conf file or any file to test with.
```
cd original
cp /etc/host.conf .
```
List the contents and take note of the inode (first column)
```
ls -ila
```
Create a symbolic link to host.conf called linkhost.conf
```
ln -s host.conf linkhost.conf
```
Now do list out the inodes
```
ln -ila
```
Notice how the inode for the link is different.

Now create a hard link to the same file
```
ln host.conf hardhost.conf
```
Now list the inoes one more time
```
ln -ila
```
Notice how the inode numbers are exactly the same for the hard link and the actual file.
Lets try some file operations now
Open up linkhost.conf and edit it and save it
Now look in host.conf and notice that the changes were made
Lets move host.conf now and see if it causes any problems
```
mv host.conf ../
```
Uh oh, now when we list the directory our link turned red lets see what is in side it
```
cat linkhost.conf
```
```
cat: linkhost.conf: No such file or directory
```
It looks like our symbolic link is now broken as it linked to a file name and not the inode. What about our hard link?
```
cat hardhost.conf
```
Looks like our hard link still works even though we moved the original file. This is because the hard link was linked to the inode the physical reference on the hard drive where the file resides. The soft link (symbolic link) on the other hand was linked to the abstract file name and was broken the moment we moved the file.

This leads to an interesting question. What happens if I delete a hard link? Even though the hard link is referenced to the physical location of the file on the hard drive though an inode, removing a hard link will not delete the original file.

Symbolic links will remain intact but will point to a non existent file.

Tutorial for links : https://www.youtube.com/watch?v=kYonC93SvpE


















