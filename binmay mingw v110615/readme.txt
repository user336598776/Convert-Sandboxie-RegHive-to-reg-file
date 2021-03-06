Binmay - command line binary search and replace
Copyright (C) 2004-2011 Sean Loaring

This program is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.


Email: info@filewut.com

Web Site: http://www.filewut.com/spages/page.php/software/binmay

Introduction
------------

Binmay is a simple, little program that searches for a string of binary in a
file or stream and replaces it with another string of binary.


Outputting Raw Binary (-p)
--------------------------

Binmay will convert a string into raw binary and dump it with the -p option:
	sloaring@dumpy:~$ binmay -p "61 62 63 64 65 66 67"|hd
	00000000  61 62 63 64 65 66 67                              |abcdefg|
	00000007


Parameter String Format (-p, -s, -S, -r, -R)
--------------------------------------------

By default all parameter strings are treated as hex.  Non-hex characters are
simply ignored:

	$ binmay -p "61-62-63-64-65-66-67"|hd
	00000000  61 62 63 64 65 66 67                              |abcdefg|
	00000007

	$ binmay -p "61-ghijklmnopqrs62-63-64-65-66-67"|hd
	00000000  61 62 63 64 65 66 67                              |abcdefg|
	00000007

Paramter strings can also be prefixed with 'h:', 'b:', 't:' and 'f:' denoting
hex, binary, text and file input.

	$ binmay -p "h:63 61 74"
	cat

	$ binmay -p 'b:01100011 01100001 01110100'
	cat

	$ binmay -p "t:this is text"
	this is text

	$ echo 'monkey' >tmpfile
	$ binmay -p "f:tmpfile"
	monkey

Searching
---------

If only the -s parameter is specified then binmay will output a list of offsets
and matching patterns, followed by a match count:

	$ binmay -p "00 00 50 00 00 00 50" | binmay -s 50
	0x2:50
	0x6:50
	Matches: 2

	$ binmay -p "00 00 50 00 00 00 50" | binmay -s 't:P'
	0x2:50 
	0x6:50 
	Matches: 2

	$ binmay -p "00 00 50 00 00 00 50" | binmay -s 'b:01010000'
	0x2:50 
	0x6:50 
	Matches: 2

	# Look look at some of the bytes surrounding an NK key prefix (6e6b) in
	# a windows registry file
	$ binmay -s 6e6b0000000000000000000000 -S 'ffff0000000000000000000000' -i ntuser.dat
	0x1024:6e 6b 2c 00 a2 56 f4 5a 7a e1 c8 01 00 
	0x11bc:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x134c:6e 6b 20 00 e6 ef 97 33 3f e1 c8 01 00 
	0x13bc:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x14c4:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x15d4:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x16dc:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x17dc:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x191c:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x19f4:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x1b04:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x1c04:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x1c64:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x1d24:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x1e24:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x1f0c:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x2024:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x214c:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	0x2234:6e 6b 20 00 8e f2 a3 c8 3e e1 c8 01 00 
	...
	0x7e228:6e 6b 00 00 28 00 03 00 04 00 ef be e9 
	0x7e274:6e 6b 20 00 d0 c7 93 49 89 e1 c8 01 00 
	0x7e2e4:6e 6b 20 00 6c 28 63 32 89 e1 c8 01 00 
	0x7e85c:6e 6b 20 00 b4 79 85 49 89 e1 c8 01 00 
	0x7e8cc:6e 6b 20 00 b4 79 85 49 89 e1 c8 01 00 
	0x7e994:6e 6b 20 00 76 65 91 49 89 e1 c8 01 00 
	0x7ed72:6e 6b 73 00 00 00 e0 ff ff ff 76 6b 04 
	0x7f17c:6e 6b 20 00 d0 6a 75 5b 89 e1 c8 01 00 
	0x7f1fc:6e 6b 20 00 d0 6a 75 5b 89 e1 c8 01 00 
	0x7f254:6e 6b 20 00 2a cd 77 5b 89 e1 c8 01 00 
	0x7f522:6e 6b 73 e4 07 00 18 00 00 00 f0 e3 07 
	Matches: 1047


Replacing
---------

Binmay can replace the strings it searches for with other strings.

To replace all instances of "ff ff af" in infile with "aa aa aa" in outfile:

	binmay -s "ff ff af" -r "aa aa aa" -i infile -o outfile

To delete all instances of "ff 00" 

	binmay -s "ff 00" -r ""

To replace all instances of "ff fX af" with "aa aa aa aa aa":

	binmay -s "ff ff af" -S "ff f0 ff" -r "aa aa aa aa aa"


Search Masking
--------------

One can apply a bitwise mask to the search string.  

	$ binmay -p "00 00 50 00 00 00 50 51 52 53" | \
	binmay -s 50 -S "f0"
	0x2:50
	0x6:50
	0x7:51
	0x8:52
	0x9:53
	Matches: 5

And everything found can be replaced

	$ binmay -p "00 00 50 00 00 00 50 51 52 53" | \
	binmay -s 50 -S "f0" -r "11" | hd
	00000000  00 00 11 00 00 00 11 11  11 11                    |..........|
	0000000a

Replace Masking
---------------

You can selectively replace sections of found strings with masks.

	$ binmay -p "00 00 50 00 00 00 50 51 52 53"| \
	binmay -s 50 -S "f0" -r "90" -R "f0" | hd
	00000000  00 00 90 00 00 00 90 91  92 93                    |..........|
	0000000a



