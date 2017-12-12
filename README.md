# Unix-Lessons


FILES, FILE TYPES, FILE OWNERSHIP,
AND FILE PERMISSIONS
Managing files under Linux is different from managing files under Windows NT/200x/
XP/Vista, and radically different from managing files under Windows 95/98. In this section,
we discuss basic file management tools and concepts under Linux. We’ll start with
specifics on some useful general-purpose commands, and then we’ll step back and look
at some background information.
Under Linux (and UNIX in general), almost everything is abstracted to a file. Originally,
this was done to simplify the programmer’s job. Instead of having to communicate
directly with device drivers, special files (which look like ordinary files to the application)
are used as a bridge. Several types of files accommodate all these file uses.
Normal Files
Normal files are just that—normal. They contain data or executables, and the operating
system makes no assumptions about their contents.
Directories
Directory files are a special instance of normal files. Directory files list the locations of
other files, some of which may be other directories. (This is similar to folders in Windows.)
In general, the contents of directory files won’t be of importance to your daily operations,
unless you need to open and read the file yourself rather than using existing applications
to navigate directories. (This would be similar to trying to read the DOS file allocation
table directly rather than using command.com to navigate directories or using the findfirst/findnext
system calls.)
113 Chapter 5: The Command Line
Hard Links
Each file in the Linux file system gets its own i-node. An i-node keeps track of a file’s
attributes and its location on the disk. If you need to be able to refer to a single file using
two separate filenames, you can create a hard link. The hard link will have the same
i-node as the original file and will, therefore, look and behave just like the original. With
every hard link that is created, a reference count is incremented. When a hard link is
removed, the reference count is decremented. Until the reference count reaches zero, the
file will remain on disk.
NOTE A hard link cannot exist between two files on separate partitions. This is because the hard
link refers to the original file by i-node, and a file’s i-node may differ among file systems.
Symbolic Links
Unlike hard links, which point to a file by its i-node, a symbolic link points to another
file by its name. This allows symbolic links (often abbreviated symlinks) to point to files
located on other partitions, even other network drives.
Block Devices
Since all device drivers are accessed through the file system, files of type block device
are used to interface with devices such as disks. A block device file has three identifying
traits:
▼ It has a major number.
■ It has a minor number.
▲ When viewed using the ls -l command, it shows b as the first character of the
permissions field.
For example,
[yyang@fedora-serverA ~]$ ls -l /dev/sda
brw-r----- 1 root disk 8, 0 2090-09-30 08:18 /dev/sda
Note the b at the beginning of the file’s permissions; the 8 is the major number, and
the 0 is the minor number.
A block device file’s major number identifies the represented device driver. When this
file is accessed, the minor number is passed to the device driver as a parameter, telling
it which device it is accessing. For example, if there are two serial ports, they will share
the same device driver and thus the same major number, but each serial port will have a
unique minor number.
 114 Linux Administration: A Beginner’s Guide
Character Devices
Similar to block devices, character devices are special files that allow you to access devices
through the file system. The obvious difference between block and character devices is
that block devices communicate with the actual devices in large blocks, whereas character
devices work one character at a time. (A hard disk is a block device; a modem is a
character device.) Character device permissions start with a c, and the file has a major
number and a minor number. For example,
[yyang@fedora-serverA ~]$ ls -l /dev/ttyS0
crw-rw---- 1 root uucp 4, 64 2007-09-30 08:18 /dev/ttyS0
Named Pipes
Named pipes are a special type of file that allows for interprocess communication.
Using the mknod command, you can create a named pipe file that one process can
open for reading and another process can open for writing, thus allowing the two to
communicate with one another. This works especially well when a program refuses
to take input from a command-line pipe, but another program needs to feed the
other one data and you don’t have the disk space for a temporary file.
For a named pipe file, the first character of its file permissions is a p. For example, if a
named pipe called mypipe exists in your present working directory (PWD), a long listing
of the named pipe file would show this:
[yyang@fedora-serverA ~]$ ls -l mypipe
prw-r--r-- 1 root root 0 Mar 16 10:47 mypipe
Listing Files: ls
Out of necessity, we have been using the ls command in previous sections and chapters
of this book. We will look at the ls command and its options in more details here.
The ls command is used to list all the files in a directory. Of more than 50 available
options, the ones listed in Table 5-2 are the most commonly used. The options can be
used in any combination.
To list all files in a directory with a long listing, type this command:
[yyang@fedora-serverA ~]$ ls -la
To list a directory’s nonhidden files that start with the letter A, type this:
[yyang@fedora-serverA ~]$ ls A*
115 Chapter 5: The Command Line
TIP Linux/UNIX is case-sensitive. For example, a file named thefile.txt is very different from a file
named Thefile.txt.
If no such file exists in your working directory, ls prints out a message telling
you so.
Change Ownership: chown
The chown command allows you to change the ownership of a file to someone else. Only
the root user can do this. (Normal users may not give away file ownership or steal ownership
from another user.) The syntax of the command is as follows:
[root@fedora-serverA ~]# chown [-R] username filename
where username is the login of the user to whom you want to assign ownership, and
filename is the name of the file in question. The filename may be a directory as well.
The -R option applies when the specified filename is a directory name. This option
tells the command to recursively descend through the directory tree and apply the new
ownership, not only to the directory itself, but also to all of the files and directories
within it.
Table 5-2. Common ls Options
Option for ls Description
-l Long listing. In addition to the
filename, shows the file size, date/time,
permissions, ownership, and group
information.
-a All files. Shows all files in the directory,
including hidden files. Names of hidden
files begin with a period.
-t Lists in order of last modified time.
-r Reverses the listing.
-1 Single-column listing.
-R Recursively lists all files and
subdirectories.
 116 Linux Administration: A Beginner’s Guide
NOTE The chown command supports a special syntax that allows you to also specify a group
name to assign to a file. The format of the command becomes chown username.groupname
filename.
Change Group: chgrp
The chgrp command-line utility lets you change the group settings of a file. It works
much like chown. Here is the format:
[root@fedora-serverA ~]# chgrp [-R] groupname filename
where groupname is the name of the group to which you want to assign filename ownership.
The filename may be a directory as well.
The -R option applies when the specified filename is a directory name. As with
chown, the -R option tells the command to recursively descend through the directory
tree and apply the new ownership, not only to the directory itself, but also to all of the
files and directories within it.
Change Mode: chmod
Directories and files within the Linux system have permissions associated with them.
By default, permissions are set for the owner of the file, the group associated with the
file, and everyone else who can access the file (also known as owner, group, and other,
respectively). When you list files or directories, you see the permissions in the first column
of the output. Permissions are divided into four parts. The first part is represented
by the first character of the permission. Normal files have no special value and are represented
with a hyphen (-) character. If the file has a special attribute, it is represented by
a letter. The two special attributes we are most interested in here are directories (d) and
symbolic links (l).
The second, third, and fourth parts of a permission are represented in three-character
chunks. The first part indicates the file owner’s permission. The second part indicates
the group permission. The last part indicates the world permission. In the context of
UNIX, “world” means all users in the system, regardless of their group settings.
Following are the letters used to represent permissions and their corresponding values.
When you combine attributes, you add their values. The chmod command is used
to set permission values.
Letter Permission Value
R Read 4
W Write 2
X Execute 1
117 Chapter 5: The Command Line
Using the numeric command mode is typically known as the octal permissions, since
the value can range from 0–7. To change permissions on a file, you simply add these
values together for each permission you want to apply.
For example, if you want to make it so that just the user (owner) can have full access
(RWX) to a file called foo, you would type
[yyang@fedora-serverA ~]$ chmod 700 foo
What is important to note is that using the octal mode, you always replace any permissions
that were set. So if there was a file in /usr/local that was SetUID and you ran the
command chmod -R 700 /usr/local, that file will no longer be SetUID. If you want
to change certain bits, you should use the symbolic mode of chmod. This mode turns out
to be much easier to remember, and you can add, subtract, or overwrite permissions.
The symbolic form of chmod allows you to set the bits of the owner, the group, or
others. You can also set the bits for all. For example, if you want to change a file called
foobar.sh so that it is executable for the owner, you can run the following command:
[yyang@fedora-serverA ~]$ chmod u+x foobar.sh
If you want to change the group’s bit to execute also, use the following:
[yyang@fedora-serverA ~]$ chmod ug+x foobar.sh
If you need to specify different permissions for others, just add a comma and its permission
symbols, as here:
[yyang@fedora-serverA ~]$ chmod ug+x,o-rwx foobar.sh
If you do not want to add or subtract a permission bit, you can use the equal (=) sign
instead of a plus (+) sign or minus (-) sign. This will write the specific bits to the file and
erase any other bit for that permission. In the previous examples, we used + to add the
execute bit to the User and Group fields. If you want only the execute bit, you would
replace the + with =. There is also a fourth character you can use: a. This will apply the
permission bits to all of the fields.
The following list shows the most common combinations of the three permissions.
Other combinations, such as -wx, do exist, but they are rarely used.
Letter Permission Value
--- No permissions 0
r-- Read only 4
Rw- Read and write 6
Rwx Read, write, and execute 7
r-x Read and execute 5
--x Execute only 1
 118 Linux Administration: A Beginner’s Guide
For each file, three of these three-letter chunks are grouped together. The first chunk
represents the permissions for the owner of the file, the second chunk represents the permissions
for the file’s group, and the last chunk represents the permissions for all users
on the system. Table 5-3 shows some permission combinations, their numeric equivalents,
and their descriptions.
Table 5-3. File Permissions
Permission Numeric Equivalent Description
-rw------- 600 Owner has read and write
permissions.
-rw-r--r-- 644 Owner has read and write
permissions; group and world
have read-only permission.
-rw-rw-rw- 666 Everyone has read and write
permissions. Not recommended;
this combination allows the file
to be accessed and changed by
anyone.
-rwx------ 700 Owner has read, write, and
execute permissions. Best
combination for programs or
executables that the owner
wishes to run.
-rwxr-xr-x 755 Owner has read, write, and
execute permissions. Everyone
else has read and execute
permissions.
-rwxrwxrwx 777 Everyone has read, write, and
execute permissions. Like the
666 setting, this combination
should be avoided.
-rwx--x--x 711 Owner has read, write,
and execute permissions;
everyone else has executeonly
permissions. Useful for
programs that you want to let
others run but not copy.
119 Chapter 5: The Command Line
FILE MANAGEMENT AND MANIPULATION
This section covers the basic command-line tools for managing files and directories.
Most of this will be familiar to anyone who has used a command-line interface—same
old functions, but new commands to execute.
Copy Files: cp
The cp command is used to copy files. It has a substantial number of options. See its man
page for additional details. By default, this command works silently, only displaying
status information if an error condition occurs. Following are the most common options
for cp:
Option for cp Description
-f Forces copy; does not ask for verification
-I Interactive copy; before each file is copied, verifies with user
Table 5-3. File Permissions (cont.)
Permission Numeric Equivalent Description
drwx------ 700 This is a directory created with
the mkdir command. Only
the owner can read and write
to this directory. Note that
all directories must have the
executable bit set.
drwxr-xr-x 755 This directory can be changed
only by the owner, but everyone
else can view its contents.
drwx--x--x 711 A handy combination for
keeping a directory worldreadable
but restricted from
access by the ls command. A
file can be read only by someone
who knows the filename.
 120 Linux Administration: A Beginner’s Guide
First, let’s use the touch command to create an empty file called foo.txt in the user
yyang’s home directory. Type
[yyang@fedora-serverA ~]$ touch foo.txt
To use the cp (copy) command to copy foo.txt to foo.txt.html, type
[yyang@fedora-serverA ~]$ cp foo.txt foo.txt.html
To interactively copy all files ending in .html to the /tmp directory, type this
command:
[yyang@fedora-serverA ~]$ cp -i *.html /tmp
Move Files: mv
The mv command is used to move files from one location to another. Files can be moved
across partitions/file systems as well. Moving files across partitions involves a copy
operation, and as a result, the move command may take longer. But you will find that
moving files within the same file system is almost instantaneous. Following are the most
common options for mv:
Option for mv Description
-f Forces move
-I Interactive move
To move a file named foo.txt.html from /tmp to your present working directory, use
this command:
[yyang@fedora-serverA ~]$ mv /tmp/foo.txt.html .
NOTE That last dot (.) is not a typo—it literarily means “this directory.”
There is no explicit rename tool, so you can use the mv command. To rename the file
foo.txt.html to foo.txt.htm, type
[yyang@fedora-serverA ~]$ mv foo.txt.html foo.txt.htm
Link Files: ln
The ln command lets you establish hard links and soft links (see “Files, File Types, File
Ownership, and File Permissions” earlier in this chapter). The general format of ln is as
follows:
[yyang@fedora-serverA ~]$ ln original_file new_file
121 Chapter 5: The Command Line
Although ln has many options, you’ll rarely need to use most of them. The most
common option, -s, creates a symbolic link instead of a hard link.
To create a symbolic link called link-to-foo.txt that points to the original file called
foo.txt, issue the command
[yyang@fedora-serverA ~]$ ln -s foo.txt link-to-foo.txt
Find a File: find
The find command lets you search for files according to various criteria. Like the tools
we have already discussed, find has a large number of options that you can read about
in its man page. Here is the general format of find:
[yyang@fedora-serverA ~]$ find start_dir [options]
where start_dir is the directory from which the search should start.
To find all files in the current directory (i.e., the “.” directory) that have not been
accessed in at least seven days, use the following command:
[yyang@fedora-serverA ~]$ find . -atime 7
Type this command to find all files in your present working directory whose names
are core and then delete them (i.e., automatically run the rm command):
[yyang@fedora-serverA ~]$ find . -name core -exec rm {} \;
TIP The syntax for the -exec option with the find command as used here can be
hard to remember sometimes, and so you can also use the xargs method instead of the
exec option used in this example. Using xargs, the command would then be written
[yyang@fedora-serverA ~]$ find . -name 'core' | xargs rm
To find all files in your PWD whose names end in .txt (i.e., files that have the .txt
extension) and are also less than 100 kilobytes (K) in size, issue this command:
[yyang@fedora-serverA ~]$ find . -name '*.txt' -size -100k
To find all files in your PWD whose names end in .txt (i.e., files that have the .txt
extension) and are also greater than 100K in size, issue this command:
[yyang@fedora-serverA ~]$ find . -name '*.txt' -size 100k
File Compression: gzip
In the original distributions of UNIX, the tool to compress files was appropriately called
compress. Unfortunately, the algorithm was patented by someone hoping to make a great
deal of money. Instead of paying out, most sites sought and found another compression 
 122 Linux Administration: A Beginner’s Guide
tool with a patent-free algorithm: gzip. Even better, gzip consistently achieves better
compression ratios than compress does. Another bonus: Recent changes have allowed
gzip to uncompress files that were compressed using the compress command.
NOTE The filename extension usually identifies a file compressed with gzip. These files typically
end in .gz (files compressed with compress end in .z).
Note that gzip compresses the file in place, meaning that after the compression process,
the original file is removed, and the only thing left is the compressed file.
To compress a file named foo.txt.htm in your PWD, type
[yyang@fedora-serverA ~]$ gzip foo.txt.htm
And then to decompress it, use gzip again with the -d option:
[yyang@fedora-serverA ~]$ gzip -d foo.txt.htm.gz
Issue this command to compress all files ending in .htm in your PWD using the best
compression possible:
[yyang@fedora-serverA ~]$ gzip -9 *.htm
bzip2
If you have noticed files with a .bz extension, these have been compressed with the
bzip2 compression utility. The bzip2 tool uses a different compression algorithm that
usually turns out smaller files than those compressed with the gzip utility, but it uses
semantics that are similar to gzip; for more information, read the man page on bzip2.
Create a Directory: mkdir
The mkdir command in Linux is identical to the same command in other flavors of UNIX,
as well as in MS-DOS. An often-used option of the mkdir command is the -p option.
This option will force mkdir to create parent directories if they don’t exist already. For
example, if you need to create /tmp/bigdir/subdir/mydir and the only directory that
exists is /tmp, using -p will cause bigdir and subdir to be automatically created along
with mydir.
Create a directory tree like bigdir/subdir/finaldir in your PWD. Type
[yyang@fedora-serverA ~]$ mkdir -p bigdir/subdir/finaldir
To create a single directory called mydir, use this command:
[yyang@fedora-serverA ~]$ mkdir mydir
123 Chapter 5: The Command Line
Remove a Directory: rmdir
The rmdir command offers no surprises for those familiar with the DOS version of the
command; it simply removes an existing directory. This command also accepts the -p
parameter, which removes parent directories as well.
For example, if you want to get rid of all the directories from bigdir to finaldir that
were created earlier, you’d issue this command alone:
[yyang@fedora-serverA ~]$ rmdir -p bigdir/subdir/finaldir
To remove a directory called mydir, you’d type this:
[yyang@fedora-serverA ~]$ rmdir mydir
TIP You can also use the rm command with th
