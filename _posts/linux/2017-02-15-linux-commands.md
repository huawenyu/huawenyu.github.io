---
layout: post
title:  "Linux commands"
date:   2017-02-15 13:31:01 +0800
categories: linux admin
tags: admin command
---

* content
{:toc}


# Howto:

## which file biggest

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


## the dir's size

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

# Commands:

## du

    du -shc <dir>
      -s, --summarize
      -h, --human-readable
      -c, --total

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
    sed -n '/ruby/p' file | sed 's/ruby/bird/g'

    $ sed '$d' file			#删除最后一行
    $ sed -n '1,$p' file		#显示第一行到最后一行
    $ sed -n '/ruby/p' file	#查询包括关键字ruby所在所有行
    $ sed -n '/\$/p' file		#查询包括关键字$所在所有行，使用反斜线\屏蔽特殊含义
    $ sed '1,3a drink tea' file	#第一行到第三行后增加字符串"drink tea"
    $ sed '1,2c Hi' file		#第一行到第二行代替为Hi
    $ sed -n '/ruby/p' file | sed 's/ruby/bird/g'    #替换ruby为bird

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

    uniq -uf 1
    'uniq'  does not detect repeated lines unless they are adjacent. You may want to sort the input first, or use 'sort -u' without 'uniq'.
    uniq -f 1      	 skip the first column
    uniq -s 8      	 skip the first 8 char
    uniq -uf 1
    uniq -c

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

first like cat:

```shell
    $ paste file1
    Linux
    Unix
    Solaris
    HPUX
    AIX

    $ paste file1 file2
    Linux   Suse
    Unix    Fedora
    Solaris CentOS
    HPUX    OEL
    AIX     Ubuntu

    $ paste -d':' - - < file1
    Linux:Unix
    Solaris:HPUX
    AIX:
```


## tree

    One way is to use patterns with the -I and -P switches:
    $ tree -f -I "bin|unitTest" -P "*.[ch]|*.[ch]pp." your_dir/

    The -f prints the full path for each file, and -I excludes the files in the pattern here separated by a vertical bar. The -P switch inlcudes only the files listed in the pattern matching a certain extension.
    $ tree -f -I "bin|unitTest" -P "*.[ch]|*.[ch]pp." your_dir/


## mkdir

    -m: 对新建目录设置存取权限,也可以用chmod命令设置;
    -p: 可以是一个路径名称。此时若路径中的某些目录尚不存在,加上此选项后,系统将自动建立好那些尚不存在的目录,即一次可以建立多个目录;


## tar

    tar zxvf mysql.tar.gz -C /home/aaa
        # tar -cvf tecmint-14-09-12.tar /home/tecmint/
        # tar cvfj Phpfiles-org.tar.bz2 /home/php
        # tar cvzf MyImages-14-09-12.tar.gz /home/MyImages
    tar zxvf mysql.tar.gz -C /home/aaa
    tar -xjf linux-2.6.38.tar.bz2 --transform 's/linux-2.6.38/linux-2.6.38.1/'
    remove one file from tar
    tar -tvf foo.tar | grep 'etc/resolv.conf'
    tar --delete -f foo.tar etc/resolv.conf

    -c ：压缩目录(create)
    -x ：解压文件
    -C : 指定目录解压
    -t ：查看 tarfile 里面的文件！
    c/x/t 仅能存在一个！不可同时存在！
    -z ：gzip 压缩
    -j ： bzip2 压缩
    -v ：显示文件
    -f ： f 之后立即接档名喔！不要再加参数！

    -p ：使用原文件的原来属性（属性不会依据使用者而变）
    -P ：可以使用绝对路径来压缩！
    -N ：比后面接的日期(yyyy/mm/dd)还要新的才会被打包进新建的文件中！
    --exclude FILE：不要将 FILE 打包！

## rar

    rar file
    tar xzf rarlinux-x64-5.0.b7.tar.gz
    sudo cp rar unrar /usr/local/bin/
    unrar x ~/Downloads/wps_symbol_fonts.rar

## find

    find -exec vs xargs
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

    find . -maxdepth 1
    find /path -name *stat				 Find every file under the directory /usr ending in "stat". 
    find /path -name '*.[ch]'				 find all *.c *.h files
    find /path -name  '*.c' -o -name '*.h'		 Find all .c or .h files.
    find /path -name  '*python*'				 Find files with the word "python" in the name.
    find /path -iname '*python*'				 Same as above but case-insensitive.
    find /path -regex '.*python.*|.*\.py'			 Regex match, more flexible. Find both .py files and files with the word "python" in the name.
    find /home -user joe					 Find every file under the directory /home owned by the user joe. 
    find /var/spool -mtime +60				 Find every file under the directory /var/spool that was modified more than 60 days ago. 
    find /tmp -name core -type f -print | xargs /bin/rm -f   	 Find files named core in or below the directory /tmp and delete them. Note that this will work incorrectly if there are any filenames containing newlines, single or double quotes, or spaces. 
    find /tmp -name core -type f -print0 | xargs -0 /bin/rm -f   	 solve filenames containing newlines, quotes or spaces problem
    find . -type f -exec file '{}' \;
    find dir1 dir2 dir3 | wc -l				 Count the number of files in multiple directories.


    find ... -exec rm {} \; 
    find ... | xargs rm -rf
    两者都可以把find命令查找到的结果删除，其区别简单的说是前者是把find发现的结果一次性传给exec选项，后者xargs命令会分批次的处理结果。
    xargs优点：由于是批处理的，所以执行效率比较高（通过缓冲方式）
    xargs缺点：有可能由于参数数量过多（成千上万），导致后面的命令执行失败

    rm不接受标准输入，所以不能用find / -name "tmpfile" ｜rm
    -exec 必须由一个 ; 结束，而因为通常 shell 都会对 ; 进行处理，所以用 \; 防止这种情况。 
    {} 可能需要写做 '{}'，也是为了避免被 shell 过滤。
    find ./ -type f -mtime +30 -exec rm -fr {} \; > /dev/null 2>&1
    -type f，表示只找file，文件类型的，目录和其他字节啥的不要
    -mtime +30 表示30天前的文件。
    -exec 把find到的文件名作为参数传递给后面的命令行，代替{}的部分 
    -exec后便跟的命令行，必须用“ \;”结束
    -ok 类似于-exec, 不同之处是处理每个文件时会同你交互确认是否处理
     /dev/null 2>&1 这样的写法.这条命令的意思是将标准输出和错误输出全部重定向到/dev/null中,也就是将产生的所有信息丢弃.
    find . -maxdepth 1 -type f -not -name '.*' -exec mv {} ./tools/ \;


# Samples

    $ stat -f /      	 Display the status of the filesystem using option –f
    $ tr a-z A-Z < employee.txt   	  Convert a file to all upper-case
    $ ac -p  	 Display total connect time of users
    $ wc -l **/*.c  	 count all file recursive
    $ pwd | tr -d ‘\n’ | xsel		 to clipboard
    $ grep -rl -w 'bar' /path/to/folder | xargs sed -i sed "s/\<bar\>/no bar/g"   	 search and replace in grep result
    $ diff -r -u a/dir/file1 b/dir/file1    	 generate the diff of two files
    ls | while read upName; do loName=`echo "${upName}" | tr '[:upper:]' '[:lower:]'`; mv "$upName" "$loName"; done    	 change all filename into lowercase

# command lists:

[Linux Tricks and Useful Commands][1][2]

    ```shell
    $ wget -c <url>    OR   $ curl -C <url>			 resume interrupted downloads 
    $ wget -rpk <url>						 mirror an entire website for offline viewing using the following command
    $ curl dict://dict.org/d:<word_to_search_for>			 find the definition of a word with
    $ host <site_url>						 Finding out the public IP of a website
    $ cat /dev/urandom > /dev/null				 put a CPU to 100% usage
    $ xset dpms force off						 Turning of the monitor to save power when there is no hardware key available to do so(say, on a laptop)
    $ <command> | xsel --clipboard				 How to copy the output of any command directly to the system X
    $ xdg-open <file_name>					 How to open an file from the command line using the default application for that file
    $ man -t <command_name> | ps2pdf - <command_name.pdf>		 How to convert an entire man page into pdf format for later viewing
    $ sed -i 8d <file_name>					 Deleting a particular line number from a given file without opening it in any editor
    $ echo "command you want to run" | at 01:00		 Running a command at a specified time
    $ sudo cat /etc/issue						 os info
    $ sudo cat /proc/cpuinfo
    $ sudo getconf LONG_BIT					 32bit or 64bit OS
    $ sudo fuser -k <file_name>					 Killing a process that has locked a particular file, when you know the file name that is locked, but don’t know which process is locking it.

    http://varunbpatil.github.io/2012/09/19/linux-tricks/#.UhuM2BJDss0
    $ dd if=mylinux.iso of=/dev/sdb bs=20M			 Creating a bootable USB disk
    $ sudo fdisk -l							 Partitioning and formatting a USB key
    $ dd if=/dev/dvd of=myfile.iso bs=2048			 Ripping a CD or DVD for local storage
    $ cat image.png compressed.zip > secret.png		 Hiding a file or directory within an image, view with $ unzip secret.png
    $ rename -v 's/([a-z]*)\.c/file_$1.cpp/' wad*.c		 batch rename all wad*.c files to extension *.cpp files
    $ find . -name '*-GHBAG-*' -exec bash -c 'mv $0 ${0/GHBAG/stream-agg}' {} \;		 another batch rename

    $ rm "-1mpFile.out"
    rm: invalid option -- '1'
    $ rm -- -1mpFile.out

    $ rm !(*.c|*.py)        	 delete all the files except .c and .py files.

    $ touch -d "9am" temp1  	 create dir according assigned time
    $ stat temp1            	 check the 'touch' result
     $ touch -t 200701310846.26 index.html    	 [[cc]yy]MMDDhhmm[.ss]
     $ touch -d '2007-01-31 8:46:26' index.html
     $ touch -d 'Jan 31 2007 8:46:26' index.html    	 you can copy the timestamp use ls -l

    $ find ~ -mtime -5 -name \.\*       	 hidden files in your home directory changed in the last 5 days
    $ find ~ -mmin 14 -name \.\*        	 has changed much more recently than the last 14 minutes

    Be aware that doing a 'ls' will affect the access time-stamps of the files shown by that action. If you do an ls to see what's in a directory and try the above to see what files were accessed in the last 14 minutes all files will be listed by find.

    To locate files that have been modified since some arbitrary date use this little trick:

    $ touch -d "13 may 2001 17:54:19" date_marker
    $ find . -newer date_marker 
    $ find . \! -cnewer date_marker     	 find files created before that date, use the cnewer and negation conditions:
    To find a file which was modified yesterday, but less than 24 hours ago:

    $ find . -daystart -atime 1 -maxdepth
    The -daystart argument means the day starts at the actual beginning of the day, not 24 hours ago. 
    This argument has meaning for the -amin, -atime, -cmin, ctime, -mmin and -mtime options.

    Finding files of a specific size

    $ find . -size 1000c           	 find files with exactly 1000 characters
    $ find . -size +599c -and -size -701c       	 find files containing between 600 to 700 characters, inclusive.

    c = bytes
    w = 2 byte words
    k = kilobytes
    b = 512-byte blocks

    Thus we can use find to list files of a certain size:

    $ find /usr/bin -size 48k

    Empty files
    You can find empty files with $ find . -size 0c 
    Using the -empty argument is more efficient.

    To delete empty files in the current directory:
    $ find . -empty -maxdepth 1 -exec rm {} \;

    $ find . -newer ../temp1 ! -newer ../temp2 -exec cp '{}' ./bkup/ ';'      	 copy newer than 'temp1' and not newer than temp2
    $ find . -newer marker -not -path "*/\.*"                	 find newer compare with marker but exclude the hidden-dir

    $ grep -l "printf" *.c     	 search all the files in a directory containing a particular string "printf"
    $ > ./logfile           	 empty a file

    $ man -k login          	 search man pages for a particular string "login"
    $ tail -f logfile logfile1   	 follow multiple log files on the go
    $ <space>cmd                  	 insert space to not appear in `history` command

    $ echo "You can simulate on-screen typing just like in the movies" | pv -qL 10        << typing simulate using pv

    $ alias ls='ls -lart'
    $ \ls                   	 skip the alias

    $ !!<space>             	 will show last command

    $ touch new_binary 
    touch: cannot touch `new_binary': Permission denied  
    $ sudo !!<space>

    $ ls -lart /home/stun/derp_test/*.py  
    $ echo !! > script.sh  
      -> echo ls -lart /home/stun/derp_test/*.py > script.sh

    $ !-2                   	 rerun the last 2nd command
    uname -a | grep Linux

    $ pushd                 	 put or pop dir into stack
    $ popd                  


    pwd -P                  # Print path to the dir you are in, converting any symlinks in the path into their real/(P)hysical directory name.
    grep -ril inactive /etc # Show matching files(-l) with the string “inactive” regardless of case(-i) in all subdirs of /etc (-r)
    ascii || man ascii      # Quick access to the ASCII character table either through the ascii program or the man page
    ionice -c 3 cp vm1.img vm1-clone.img # Copy a file using “ionice -c 3″ to give it idle IO priority to reduce load on the system.
    foremost                # file recovery program
    dd if=/dev/sdc of=sdc.img bs=100M conv=sync,noerror # Image a disk
    ls -d */                # View only the directories in the current directory
    gzip -l largefile.gz    # A fast way to get the size of the uncompressed gzip file
    find dir1 dir2 dir3 | wc -l     # Count the number of files in multiple directories
    ls -1 | tr A-Z a-z      # List files in lowercase so can compare with another list
    sleep 8h 30m 20         # sleep mixed time intervals
    sed -n ’1,10p’          # put something like this into a script called headnoclose.sh
    df .                    # find out what partition the current directory is on
    ls -l |sed ‘s/^/ /’     # This intents the output.

    service network start && service httpd start # Use && when you want to run a command after another only if the first one is successful
    sleep 30m ; killall tcpdump      # Use a ; in a statement if you want to run a command after another, but don’t care about the exit status of 1st
    ssh-keygen -R servername      # Remove the host key for ‘servername’ from your known_hosts file
    ssh-copy-id ‘user@remotehost’    # Automatically installs your public key to the remote host (this actually is included in the openssh package)
    xargs -n1 -0 < /proc/$(pidof firefox| cut -d’ ‘ -f1)/environ       # Print the environment of a running process (ie. firefox) 
    awk ‘{if (a[$1]) { print; } else { a[$1]=1 }}’ md5sums.txt         # And here is a way to print only duplicates using just awk. sort md5sums.txt | uniq -d -w 32 # Print only duplicate md5sums by looking only at first 32 chars for uniqueness. head -5 file1 |cat – file2 >combofile # One way of putting the first 5 lines of file1 before the contents of file2 and writing to combofile
    last |awk ‘BEGIN {f=0;u=”YOURUSER”;} {if ($1==u && f==0){f=1; print $0;}else if(f==1 && $1!=u) {print $0;exit;}}’       # Display prev. user b4 u
    grep -B2 Subj: maildrop.log | grep -v ^From: | grep -v ^– | xargs -d$’\n’ -n 2        # Join the Date and Subj line together in procmail log.
    find . -type f | egrep -o “[^\.]+$” | tr A-Z a-z | sort | uniq -c  # Show a count of all the file extensions in use below current directory.
    awk ‘{a[$1] += $10} END {for (h in a) print h ” ” a[h]}’ access_log | sort -k 2 -nr | head -10          # Display top bandwidth hogs on website.

    df -TP | grep -E ” ext[34] ” | awk {‘print $NF’} | sed ‘s/.*/disk & 5%/’ # Print out the partition table and format it for snmpd.conf
    df -TP | awk ‘$2=/ext[34]/ {print “disk ” $NF ” 5%”}’ # snmpd disk entries, same thing but let awk do most of the processing work.
    df -P -t ext3 -t ext4 | grep / | awk ‘{print “disk ” $NF ” 5%”}’ # snmpd disk entries, Let df select the filesystems directly.
    df -TP | grep -E ” ext[34] ” | rev | cut -d’ ‘ -f1 | rev | while read -r fs ; do echo “disk $fs 5%” ; done # Disk entries. The Crazy Way!

    ethtool -p eth0           # Blink eth0′s LED so you can find it in the rat’s next of server cables. Ctrl-C to stop. Thanks

    ls / fake 2>&1 > /dev/null | grep “cannot”         # Redirect stdout to /dev/null, and make stderr go through the pipe.
    ps aux | awk ‘/firefox/ {sum += $6} END { printf “%dMB\n”, sum/1024 }’         # Show the total memory used by Firefox processes (Probably a lot)

```

See for [more info][3].

  [1]: http://linuxtrove.com/wp/?p=314
  [2]: http://57un.wordpress.com/2013/04/29/15-interesting-and-extremely-helpful-linux-cli-tricks/
  [3]: http://www.cyberciti.biz/faq/linux-unix-test-internet-connection-download-upload-speed/
