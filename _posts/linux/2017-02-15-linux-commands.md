---
layout: post
title:  "Linux commands"
date:   2017-02-15 13:31:01 +0800
categories: linux admin
tags: admin command
---

* content
{:toc}


# terminal application

## [great-terminal-replacements-for-gui-applications][5]

# Howtos

## file order-by-size

If you want to find all files in the current directory and its sub directories and list them according to their size (without considering their path), and assuming none of the file names contain newline characters, with GNU `find`, you can do this:

    find . -type f -printf "%s\t%p\n" | sort -n


From `man find` on a GNU system:

       -printf format
              Unlike  -print, -printf  does  not add a newline at the end of
              the string.  The escapes and directives are:

              %p     File's name.
              %s     File's size in bytes.

From `man sort`:

       -n, --numeric-sort
              compare according to string numerical value


## current dir size

    du -shc .

## clean logfile

Clean logfile which come from `plink -telnet 127.0.0.1 | tee log.file`

    # Removing non printable characters
    $ tr -dc '[:print:]\n' < log.file
       -c, -C, --complement
              use the complement of SET1

       -d, --delete
              delete characters in SET1, do not translate

    # also remove escape sequences as well
    $ sed -r "s/\x1B\[([0-9]{1,2}(;[0-9]{1,2})?)?[m|K]//g"

## Test Internet speed
### Download speed

    lftp -e `pget http://example.com/file.iso; exit;`

### Upload speed

    lftp -u userName ftp.example.com -e `put largecd1.avi; bye`

### Get network throughput rate:

    iperf -s -B serverIP
    iperf -c serverIP -d -t 60 -i 10

## ssh with password

    $ sudo apt-get install sshpass
    $ sshpass -p your_password ssh user@hostname

# command line

## Command Line Shortkey

[Command Line Shortkey][6]

```
    CTRL + A                <<< Move to the beginning of the line
    CTRL + E                <<< Move to the end of the line

    CTRL + U                <<< not work linux, Clear the characters on the line before the current cursor position
    CTRL + K                <<< Clear the characters on the line after the current cursor position

    CTRL + _                <<< Undo the last change

    Others
    CTRL + [left arrow]     <<< Move one word backward (on some systems this is ALT + B)
    CTRL + [right arrow]    <<< Move one word forward (on some systems this is ALT + F)
    ESC + [backspace]       <<< Delete the word in front of the cursor
    Alt + [backspace]       <<< Delete the word in front of the cursor
    CTRL + W                <<< Delete the word in front of the cursor
    ALT + D                 <<< Delete the word after the cursor

    CTRL + L                <<< Clear screen
    CTRL + S                <<< Stop output to screen
    CTRL + Q                <<< Re-enable screen output
    CTRL + C                <<< Terminate/kill current foreground process
    CTRL + Z                <<< Suspend/stop current foreground process
```

## command history

[Command History][7]

```
    history n               <<< This will only list the last n commands. 

    CTRL + R                <<< Search history, hit ctrl-r again to scroll backward through the matched history
    CTRL + S                <<< scroll forward through the matched history
    CTRL + G, or left,right <<< Escape from search mode
    !abc                    <<< Echo last command in history beginning with abc
    !abc:p                  <<< Echo last command in history beginning with abc
    !?abc                   <<< Echo the last command that contains abc
    !?abc?                  <<< Echo the most recent command that contains string.

    !!                      <<< Echo last whole command
    !* <OR> !^ <OR> !$  <OR> !:2    <<< Echo last command’s parameters <ALL>,  <first>,  <last>, <2nd>
    Alt+.  <OR>  Alt+_      <<< Echo last command’s parameters: Press esc-. or alt+., tt cycles through the previous arguments you used.

    ls -l !cp:2             <<< !cp:2 echo command starts with cp and takes the second argument

    $ ls | sed ... | source /dev/stdin   <<< execute the output of a command within the current shell?
    $ eval $( ls | sed... )
```


## tricks

```
    $ gnome-session-quit	<<< shell’s logout command
    $ reset		<<< shell reset
    $ setxkbmap	<<< reset the keyboard map
    $ bash -xv filesize.sh   <<<< Execute Shell script with debug option
```

## shell wildcard:

```
    ?       do you have this character, zero or one ?
    .        yes, I have just one.
    +       yes, I have at least one, one or more.
    *        who knows, have or none.
    ^        begin with
    [^]      not set
    $       end with
```

## shell Multiline

### 1. Passing multiline string to a variable:

```
    Examples of Bash cat <<EOF syntax usage:

    $ sql=$(cat <<EOF
    SELECT foo, bar FROM db
    WHERE foo='baz'
    EOF
    )
```

The $sql variable now holds newlines as well, you can check it with echo -e "$sql" cmd:

    $ echo $sql

### 2. Passing multiline string to create a script file:

```
    $ cat <<EOF > print.sh
    #!/bin/bash
    echo \$PWD
    echo $PWD
    EOF
```

The print.sh file now contains:

    #!/bin/bash
    echo $PWD
    echo /home/user

### 3. Passing multiline string to a command/pipe:

```
    $ cat <<EOF | grep 'b' | tee b.txt | grep 'r'
    foo
    bar
    baz
    EOF
```

# Commands:

## tool: mutt - email client

    echo "" | mutt -s 'patch of HTTP-CONNECT-USER' hyu@fortinet.com -a patch.diff

## tool: trans - dictionary client

[Page][https://github.com/soimort/translate-shell]

    $ wget git.io/trans
    $ chmod +x ./trans

    $ trans :zh word
    $ trans en:zh "word processor"

    ### Translate a word into Chinese and Japanese
    $ trans :zh+ja word

    $ trans zh: 手紙

## watch

### sample

```sh
    # every 5-seconds execute the command
    watch    -n 5 free -m
    watch -t -n 5 free -m               <=== -t silent the header
    watch -d=cumulative 'ls -l | fgrep janis'
```

### shell-script likes

You can emulate the basic functionality with the shell loop:

    while :; do clear; your_command; sleep 2; done

That will loop forever, clear the screen, run your command, and wait two seconds - the basic `watch your_command` implementation.

You can take this a step further and create a `watch.sh` script that can accept `your_command` and `sleep_duration` as parameters:

```sh
    #!/bin/bash
    # usage: watch.sh <your_command> <sleep_duration>

    while :;
      do
      clear
      date
      $1
      sleep $2
    done
```

## du

    du -shc <dir>
      -s, --summarize
      -h, --human-readable
      -c, --total

    df .                    # find out what partition the current directory is on

## dd

    $ dd if=mylinux.iso of=/dev/sdb bs=20M	# Creating a bootable USB disk
    $ dd if=/dev/dvd of=myfile.iso bs=2048	# Ripping a CD or DVD for local storage
    $ dd if=/dev/sdc of=sdc.img bs=100M conv=sync,noerror	# Image a disk

## ls

    $ ls -d forti*
      forticov  fortipkg  fortitest  fortiweb

    $ ls forti*
      forticov:
      coretypes.h  defaults.h  filenames.h  gcov-io.c  gcov-io.h  gcov-iov.h  libgcov.c  Makefile  obj  Rules.mk  script  tm.h  tsystem.h

      fortipkg:
      fetch  include  lib  packages

      fortitest:
      appl  asic  common  include  init  linux  Makefile  netbt  obj  osdep  platform  Rules.mk  testcase  tools

      fortiweb:
      client  extutil  genoem.sh  genpy.py  include  java  Makefile  Makefile.gui  modules  obj  packages  python  Rules.mk  server  tools  ujson

With `zsh` you can easily express it directly, e.g:

    echo *(.)

will either only return the list of regular files or an error depending on your configuration.

For the non-directories:

    echo *(^/)

(will include symlinks (including to directories), named pipes, devices, sockets, doors...)

    echo *(-.)

for regular files and symlinks to regular files.

    echo *(-^/)

for non-directories and no symlinks to directories either.

Also, see the `D` globbing qualifier if you want to include <strong>D</strong>ot files (hidden files), like `*(D-.)`.

## grep; ag,ack

    $ grep -Inr "60D"
    $ ag --nogroup "kernel" cooked

    # cut long lines and keep color
    $ ag --nogroup --color "kernel" cooked | cut -c1-200

## rm

    $ rm "-1mpFile.out"
    $ rm -- -1mpFile.out
    $ rm !(*.c|*.py)        	 delete all the files except .c and .py files.

## diff

There have another tools like: lsdiff, filterdiff, diffstat, patch.

    $ diff -x '*.gz' -x '.git' -x '.svn' -w -u -r -N WorkingCopy1 WorkingCopy2
      -w to ignore all Whitespace
      -u to use the unified diff format (like subversion)
      -r for recursive
      -N to let new files appear in the patch

## touch

    $ touch -d "9am" temp1  	 create dir according assigned time
    $ stat temp1            	 check the 'touch' result
    $ touch -t 200701310846.26 index.html    	 [[cc]yy]MMDDhhmm[.ss]
    $ touch -d '2007-01-31 8:46:26' index.html
    $ touch -d 'Jan 31 2007 8:46:26' index.html    	 you can copy the timestamp use ls -l
    $ touch -d "13 may 2001 17:54:19" date_marker

## cut

    cut -d: -f 1,3
      -d, --delimiter
      -f, --show-field
      -c, --show-character

```shell
    $ cut -d: -f 1 names.txt		 show 1st field with a colon delimited
    $ cut -d: -f 1,3 names.txt 		 show 1st and 3rd field from a colon delimited file
    $ cut -c 1-8 names.txt			 show 1~8 characters of every line in a file
    $ cut -c 5-  log.txt			 remove first 5 characters, show the reserved
    $ free | tr -s ' ' | sed '/^Mem/!d' | cut -d" " -f2    	 Displays the total memory available on the system.
```

## split

    # You can use the split command with the -b option:
    $ split -b 1024m file.tar.gz

    # Creates files: file.tar.gz.part-aa, file.tar.gz.part-ab, file.tar.gz.part-ac, ...
    $ split -b 1024m file.tar.gz "file.tar.gz.part-"

### reassembled

    <windows>
    $ copy /b file1 + file2 + file3 + file4 filetogether

    <linux>
    $ cat file.tar.gz.part-* > file.tar.gz

### with tar

    # create archives
    $ tar cz my_large_file_1 my_large_file_2 | split -b 1024MiB - myfiles_split.tgz_

    # uncompress
    This solution avoids the need to use an intermediate large file when (de)compressing. Use the tar -C option to use a different directory for the resulting files. btw if the archive consists from only a single file, tar could be avoided and only gzip used:
    $ cat myfiles_split.tgz_* | tar xz

### with gzip

    # create archives
    $ gzip -c my_large_file | split -b 1024MiB - myfile_split.gz_

    # uncompress
    $ cat myfile_split.gz_* | gunzip -c > my_large_file
    For windows you can download ported versions of the same commands or use cygwin.

## awk

```shell
    awk -F ":" '/wilson/{ print $1 | "sort" }' /etc/passwd

    awk '/regex/'				 Print only lines which match regular expression (emulates "grep")
    awk '!/regex/'				 Print only lines which do NOT match regex (emulates "grep -v")
    awk 'NR==1; END{print}'		 print the first and last line

    awk '/AAA.*BBB.*CCC/'			 AND, contain "AAA" and "BBB", and "CCC" in this order.

    awk '/AAA|BBB|CCC/'			 OR, match any of "AAA" or "BBB", or "CCC".
    awk '/AAA/; /BBB/; /CCC/'		 AND, Grep for AAA and BBB and CCC (in any order)

    awk 'NR==8,NR==12'			 Region, Print section of file based on line numbers (lines 8-12, inclusive, emulates sed -n ‘8,12p’)
    awk '/Iowa/,/Montana/'		 Print section of file between two regular expressions (inclusive), (emulates sed -n ‘/Iowa/,/Montanna/p’)

    awk 'END{print}'				 Print the last line of a file (emulates "tail -1")
    awk 'NR < 11'				 Print first 10 lines of file (emulates behavior of "head")

    awk '!a[$0]++'				 Remove duplicate, consecutive lines (emulates "uniq")
    awk 'a !~ $0; {a=$0}'			 

    awk -F ":" '{ print $1 | "sort" }' /etc/passwd		 Print and sort the login names of all users
    awk '{gsub(/scarlet|ruby|puce/, "red"); print}'		 Change "scarlet" or "ruby" or "puce" to "red"
    awk '{sub(/^/, "     ");print}'					 Insert 5 blank spaces at beginning of each line (make page offset)
    awk '/regex/{getline;print}'					 Print the line immediately after a regex, but not the line containing the regex
    awk '{total = total + $1}END{print total}'
```

## sed

```shell
    $ sed 's/ruby/bird/g'		# replace like vim
    $ sed '$d' file				# delete last line
    $ sed -n '1,$p' file		# display all
    $ sed -n '/ruby/p' file		# grep keyword
    $ sed '1,3a drink tea' file	# append "drink tea" to the 1~3 line
```

## sort

```shell
    sort -t: -u -k 3n

    $ sort -r names.txt			Sort a text file in descending order
    $ sort -t: -k 2 names.txt		Sort a colon delimited text file on 2nd field (employee_id)
    $ sort -t: -u -k 3 names.txt	Sort a tab delimited text file on 3rd field (department_name) and suppress duplicates
    $ sort -t: -k 3n /etc/passwd | more	 Sort the passwd file by the 3rd field (numeric userid)
    $ sort -t . -k 1,1n -k 2,2n -k 3,3n -k 4,4n /etc/hosts		Sort /etc/hosts file by ip-address

    ps –ef | sort				Sort the output of process list
    ls -al | sort +4n			List the files in the ascending order of the file-size. i.e sorted by 5th filed and displaying smallest files first.
    ls -al | sort +4nr			List the files in the descending order of the file-size. i.e sorted by 5th filed and displaying largest files first.
```

## uniq

`uniq` does not detect repeated lines unless they are adjacent. You may want to sort the input first, or use 'sort -u' without 'uniq'.

```shell
    uniq -uf 1
    uniq -f 1		skip the first column
    uniq -s 8		skip the first 8 char
    uniq -uf 1
    uniq -c

    # statitics app crash times
    ag --nogroup " application " Mar_16.txt.txt | awk '{print $6}' | sort | uniq -c
```

## join

like excel’s vlookup

## bc

```shell
    echo $(sed 's/$/+/' /tmp/file.txt) 0 | bc
    echo will remove newline, default have -n option which donnot show the newline.
    Also we can use paste -s to remove a file’s newline
    paste -sd+ - | bc
    http://www.theunixschool.com/2012/07/10-examples-of-paste-command-usage-in.html 
```

## paste

merge two files by column

```shell
    $ paste file1	# like cat
    Linux
    Unix

    $ paste file1 file2
    Linux   Suse
    Unix    Fedora

    $ paste -d':' - - < file1
    Linux:Unix
    Solaris:HPUX
```

## tree

    $ tree -f -I "bin|unitTest" -P "*.[ch]|*.[ch]pp." your_dir/
        -f prints the full path for each file,
        -I excludes the files in the pattern here separated by a vertical bar.
        -P switch inlcudes only the files listed in the pattern matching a certain extension.

## mkdir

    -m: setup file mode
    -p: create the whole path


## tar

    tar cvjf Phpfiles-org.tar.bz2 /home/php
    tar cvzf MyImages-14-09-12.tar.gz /home/MyImages

    tar zxvf mysql.tar.gz -C /home/aaa		# uncompress to the assigned dir
    tar -xjf linux-2.6.38.tar.bz2 --transform 's/linux-2.6.38/linux-2.6.38.1/'

    tar -tvf foo.tar | grep 'etc/resolv.conf'	# list file
    tar --delete -f foo.tar etc/resolv.conf	# remove one file from tar

        c/x/t only exist one-of-them
        -c, compress/create
        -x, uncompress
        -C, uncompress to this dir
        -t, list files

        -z gzip
        -j bzip2
        -v verbose
        -f filename

## rar

    rar file
    tar xzf rarlinux-x64-5.0.b7.tar.gz
    sudo cp rar unrar /usr/local/bin/
    unrar x ~/Downloads/wps_symbol_fonts.rar

## dns & reverse-dns

    $ dig +noall +answer www.gnu.org
    www.gnu.org.            67      IN      CNAME   gnu.org.
    gnu.org.                67      IN      A       199.232.41.10

    The IP address is displayed in the A record, and is 199.232.41.10.
    The +noall, +answer combination basically tells dig to only report the answer of the DNS query and skip the rest of the output.

    ### -x option to do a reverse DNS lookup
    $ dig +noall +answer -x 199.232.41.10
    10.41.232.199.in-addr.arpa. 36000 IN    CNAME   rev-c41-10.gnu.org.
    rev-c41-10.gnu.org.       300     IN      PTR     www.gnu.org.

## locate

    The locate command is lightning fast because there is a background process that runs on your system that continuously finds new files and stores them in a database.
    When you use the locate command, it then searches that database for the filename instead of searching your filesystem while you wait (which is what the find command does).

## find

### -exec '\;'

If you run find with exec,
  - '{}' expands to the filename of each file or directory found with find.
  - ';'  the semicolon ends the command executed by exec. It needs to be escaped with \ so that the shell you run find inside does not treat it as its own special character, but rather passes it to find.

### -exec '+'

Also, find provides some optimization with exec cmd {} +
  - find appends found files to the end of the command rather than invoking it once per file (so that the command is run only once, if possible).

The difference in behavior (if not in efficiency) is easily noticeable if run with ls, e.g.

    find ~ -iname '*.jpg' -exec ls {} \;
    # vs
    find ~ -iname '*.jpg' -exec ls {} +

Assuming you have some jpg files (with short enough paths), the result is one line per file in first case and standard ls behavior of displaying files in columns for the latter.

### samples

    -a*,-c*,-m*: access, attr, modify
        [a] access (read the file's contents) - atime
        [c] change the status (modify the file or its attributes) - ctime
        [m] modify (change the file's contents) - mtime

    -mmin, -mtime: mins, days
        -mmin n   	  n minutes ago.
        -mtime n   	 days,  n*24 hours ago.
        -mtime -6,6,+6: > , ==, < ago
        -mtime +60 	 means you are looking for a file modified 60 days ago.
        -mtime -60  	 means less than 60 days.
        -mtime 60 If you skip + or - it means exactly 60 days.

        $ find . -daystart -atime 1 -maxdepth
        $ find . -newer date_marker
        $ find . \! -cnewer date_marker

    # find empty file
    $ find . -size 0c
    $ find . -empty -maxdepth 1 -exec rm {} \;

    $ find . -size 1000c	# find files with exactly 1000 characters
    $ find . -size +599c -and -size -701c	# find files containing between 600 to 700 characters, inclusive.
    $ find /usr/bin -size 48k
        c = bytes
        w = 2 byte words
        k = kilobytes
        b = 512-byte blocks

    find . -maxdepth 1
    find /path -name '*.[ch]'			# or
    find /path -name  '*.c' -o -name '*.h'	# or
    find /path -iname '*python*'		# case-insensitive.
    find /path -regex '.*python.*|.*\.py'	# Regex match
    find /home -user joe			# owned by `joe`
    find /tmp -name core -type f -print0 | xargs -0 /bin/rm -f
    find . -type f -exec file '{}' \;

    find . -maxdepth 3 -name rb_genco -exec grep -H "123268" {} \;
    find -H . -maxdepth 3 -name rb_genco | xargs -I{} grep -lsn "123268" {}

    $ find dir1 dir2 dir3 | wc -l		# Count files
    $ find . -newer ../temp1 ! -newer ../temp2 -exec cp '{}' ./bkup/ ';'	# copy newer than 'temp1' and not newer than temp2
    $ find . -newer marker -not -path "*/\.*"	# find newer compare with marker but exclude the hidden-dir

### batch rename

batch rename all wad*.c files to extension *.cpp files
    $ find . -name '*-GHBAG-*' -exec bash -c 'mv $0 ${0/GHBAG/stream-agg}' {} \;
    $ rename -v 's/([a-z]*)\.c/file_$1.cpp/' wad*.c

### find -exec vs xargs

    find ... -exec rm {} \;		# runs cmd 'rm' once for each match
    find ... -exec rm {} +		# new version like 'xargs'
    find ... -print0 | xargs -0 rm -rf	# more efficient, runs cmd 'rm' as few times as possible

### Using semicolon (;) vs plus (+) with exec in find

    This might be best illustrated with an example. Let's say that find turns up these files:
    file1
    file2
    file3

    Using -exec with a semicolon (find . -exec ls '{}' \;), will execute

    ls file1
    ls file2
    ls file3

    But if you use a plus sign instead (find . -exec ls '{}' \+), all filenames will be passed as arguments to a single command:
    ls file1 file2 file3

## curl

[Doc](https://ec.haxx.se/http-cookies.html)

### Install new verion (ubuntu)

1. Append to /etc/apt/sources.list like this:

deb http://security.ubuntu.com/ubuntu zesty-security main
deb http://cz.archive.ubuntu.com/ubuntu zesty main

2. Install

$ sudo apt-get update
$ sudo apt-get install libcurl3  ### if needed, maybe remove them first
$ sudo apt-get install curl

# Samples

    $ stat -f /      	 Display the status of the filesystem using option –f
    $ tr a-z A-Z < employee.txt   	  Convert a file to all upper-case
    $ ac -p  	 Display total connect time of users
    $ wc -l **/*.c  	 count all file recursive
    $ pwd | tr -d ‘\n’ | xsel		 to clipboard
    $ grep -rl -w 'bar' /path/to/folder | xargs sed -i sed "s/\<bar\>/no bar/g"   	 search and replace in grep result
    $ diff -r -u a/dir/file1 b/dir/file1    	 generate the diff of two files

    # change all filename into lowercase
    $ ls | while read upName; do loName=`echo "${upName}" | tr '[:upper:]' '[:lower:]'`; mv "$upName" "$loName"; done

# command lists:

[Linux Tricks and Useful Commands][1][2][4]

```shell
    $ wget -c <url>			# resume interrupted downloads
    $ curl -C <url>			# resume interrupted downloads
    $ wget -rpk <url>			# mirror an entire website
    $ curl dict://dict.org/d:<word>
    $ host <url>			# public IP
    $ cat /dev/urandom > /dev/null	# put a CPU to 100% usage
    $ xset dpms force off		# Turning of the monitor to save power
    $ <command> | xsel --clipboard	# put output into the X system clipboard
    $ echo "command you want to run" | at 01:00		# Running a command at a specified time

    $ sudo cat /etc/issue		# os info
    $ sudo cat /proc/cpuinfo
    $ sudo getconf LONG_BIT		# 32bit or 64bit OS
    $ uname -a | grep Linux
    $ sudo fuser -k <file_name>		# Killing a process that has locked a particular file

    $ sudo fdisk -l			# Partitioning and formatting a USB key
    $ cat image.png compressed.zip > secret.png		# Hiding a file or directory within an image, view with $ unzip secret.png


    $ grep -l "printf" *.c     	 search all the files in a directory containing a particular string "printf"
    $ grep -B2 Subj: maildrop.log | grep -v ^From: | grep -v ^– | xargs -d$’\n’ -n 2        # Join the Date and Subj line together in procmail log.
    $ grep -ril inactive /etc # Show matching files(-l) with the string “inactive” regardless of case(-i) in all subdirs of /etc (-r)
    $ > ./logfile           	 empty a file

    $ man -k login          	 search man pages for a particular string "login"
    $ tail -f logfile logfile1   	 follow multiple log files on the go
    $ <space>cmd                  	 insert space to not appear in `history` command

    # typing simulate using pv
    $ echo "You can simulate on-screen typing just like in the movies" | pv -qL 10

    $ pushd                 	 put or pop dir into stack
    $ popd

    $ pwd -P                  # Print path to the dir you are in, converting any symlinks in the path into their real/(P)hysical directory name.
    $ ascii || man ascii      # Quick access to the ASCII character table either through the ascii program or the man page
    $ ionice -c 3 cp vm1.img vm1-clone.img # Copy a file using “ionice -c 3″ to give it idle IO priority to reduce load on the system.
    $ foremost                # file recovery program
    $ ls -d */                # View only the directory
    $ gzip -l largefile.gz    # A fast way to get the size of the uncompressed gzip file

    $ sleep 30m ; killall tcpdump      # Use a ; in a statement if you want to run a command after another, but don’t care about the exit status of 1st
    $ ssh-keygen -R servername      # Remove the host key for ‘servername’ from your known_hosts file
    $ ssh-copy-id ‘user@remotehost’    # Automatically installs your public key to the remote host (this actually is included in the openssh package)

    $ xargs -n1 -0 < /proc/$(pidof firefox| cut -d’ ‘ -f1)/environ       # Print the environment of a running process (ie. firefox) 
    $ last |awk ‘BEGIN {f=0;u=”YOURUSER”;} {if ($1==u && f==0){f=1; print $0;}else if(f==1 && $1!=u) {print $0;exit;}}’       # Display prev. user b4 u
    $ find . -type f | egrep -o “[^\.]+$” | tr A-Z a-z | sort | uniq -c  # Show a count of all the file extensions in use below current directory.
    $ awk ‘{a[$1] += $10} END {for (h in a) print h ” ” a[h]}’ access_log | sort -k 2 -nr | head -10          # Display top bandwidth hogs on website.
    $ awk ‘{if (a[$1]) { print; } else { a[$1]=1 }}’ md5sums.txt         # And here is a way to print only duplicates using just awk. sort md5sums.txt | uniq -d -w 32 # Print only duplicate md5sums by looking only at first 32 chars for uniqueness. head -5 file1 |cat – file2 >combofile # One way of putting the first 5 lines of file1 before the contents of file2 and writing to combofile
    $ ps aux | awk ‘/firefox/ {sum += $6} END { printf “%dMB\n”, sum/1024 }’         # Show the total memory used by Firefox processes (Probably a lot)

    $ df -TP | grep -E ” ext[34] ” | awk {‘print $NF’} | sed ‘s/.*/disk & 5%/’ # Print out the partition table and format it for snmpd.conf
    $ df -TP | awk ‘$2=/ext[34]/ {print “disk ” $NF ” 5%”}’ # snmpd disk entries, same thing but let awk do most of the processing work.
    $ df -P -t ext3 -t ext4 | grep / | awk ‘{print “disk ” $NF ” 5%”}’ # snmpd disk entries, Let df select the filesystems directly.
    $ df -TP | grep -E ” ext[34] ” | rev | cut -d’ ‘ -f1 | rev | while read -r fs ; do echo “disk $fs 5%” ; done # Disk entries. The Crazy Way!

    $ ethtool -p eth0           # Blink eth0′s LED so you can find it in the rat’s next of server cables. Ctrl-C to stop. Thanks

## Howtos extra

### How to Disable Invalid SSL in Firefox

Secure Socket Layer, or SSL, protects private data by encrypting it during transmission.
To enable SSL encryption, websites use an SSL certificate issued by a certificate authority.
When you enter an "https://" address to browse a site, Firefox checks the SSL certificate data to determine whether it is valid.
When Firefox encounters an expired or invalid SSL certificate, it blocks the page or displays a warning.
However, you might want to disable the warnings or blocking of unencrypted sites for site testing or other reasons.
A change to advanced Firefox preferences allows you to prevent the warnings.

- about:config
- browser.ssl_override_behavior" in the "Filter" box. Double-click the "browser.ssl_override_behavior" value in the "Preference Name" list.
- Change the value in the "Enter integer value" dialog box from "2" to "1" and click the "OK" button.

```

  [1]: http://linuxtrove.com/wp/?p=314
  [2]: http://57un.wordpress.com/2013/04/29/15-interesting-and-extremely-helpful-linux-cli-tricks/
  [3]: http://www.cyberciti.biz/faq/linux-unix-test-internet-connection-download-upload-speed/
  [4]: http://varunbpatil.github.io/2012/09/19/linux-tricks/#.UhuM2BJDss0
  [5]: http://www.tuxarena.com/2014/03/20-great-terminal-replacements-for-gui-applications/
  [6]: http://www.thegeekstuff.com/2008/08/15-examples-to-master-linux-command-line-history/
  [7]: http://www.tldp.org/LDP/GNU-Linux-Tools-Summary/html/x1712.htm

