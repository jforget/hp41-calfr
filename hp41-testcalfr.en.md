-*- encoding: utf-8; indent-tabs-mode: nil -*-

# French Revolutionary Calendar for HP-41, tests

## Introduction

These programs for HP-41CV and HP-41CX give a test suite for <tt>CALFR</tt>.


## Instructions for Use

If you have installed the HP-41CX variant of the conversion program,
you will have to use the file <tt>testcf</tt><var>n</var><tt>dmy.txt</tt>
and <tt>testcf</tt><var>n</var><tt>mdy.txt</tt>, with <var>n</var> from 1 to 4.
Do not use the files with the number 5.

If you have installed either HP-41CV variant, use the test files with
the corresponding <tt>DMY</tt> or <tt>MDY</tt> format.


Set-up you HP-41CX in <tt>DMY</tt> or <tt>MDY</tt> mode, depending on which
test file you run. This step is not necessary on the HP-41CV, because
the date format is hard-coded. Run the program.
If it stops with a <tt>TONE&nbsp;0</tt>, there has been an
error. The Gregorian date is in register X, the expected 
French Revolutionary date is in register alpha.

If the program has stopped, you can press <tt>R/S</tt> to continue
with the following tests. At the end, the program displays 
<tt>SUCCES</tt> for success, of <tt>ECHEC</tt> for failure. 
The binary indicator 0 is another way to signal failure.


## Installation

### Prerequisite

These programs require a HP-41CX or a HP-41CV.
You must have <tt>CALFR</tt> installed on your HP-41.

The programs use the memory registers 10 to 12, while
<tt>CALFR</tt> uses memory registers 00 to 06.

There is no synthetic programming in this program.

Because of memory capacity reasons, the test suite is split
into several programs instead of just one.


## Real HP-41

The only way to enter these programs into a real-world HP-41
is by typing them. But is it worth it?

Although, if you have a disk drive that
can be used with HP-IL or a barcode reader, you can have a
look at the
<a href="http://www.hpmuseum.org/software/41uc.htm#wand">barcode generator</a>,
the <a href="http://www.hpmuseum.org/software/41uc.htm#sdt">shuttle disk method</a>
or the <a href="http://www.hpmuseum.org/software/41uc.htm#dlt">direct link technique</a>
in the documentation of the <a href="http://www.hpmuseum.org/software/41uc.htm">hp41uc</a> tool.


## v41

For the v41 emulator, use the
<a href="http://www.hpmuseum.org/software/41uc.htm">hp41uc</a>
tool to convert the text files into raw format.
You should have to just type:

<pre>
hp41uc /t=testcf1dmy.txt /r=testcf1dmy.raw
</pre>

Except that it does not work. There are two reasons for this.
First, hp41uc knows only 8.3 filenames, it cannot deal with
longer filenames. Then, in a source file, you cannot type more
than one HP-41 instruction per line. The documentation explains
that you can write several instructions in a single line, provided
you separate them with "comma+space". I have tried, it does not
work. So, if you have Cygwin and Perl, you can use this script:


### `tst-cmp`

```
#!/bin/sh
# -*- encoding: utf-8; indent-tabs-mode: nil -*-
#
# Compiling the test files, which have names outside the 8.3 convention.
# Compilation des fichiers de test qui ont le défaut de ne pas avoir un nom 8.3.
#

for i in 1 2 3 4 5
do
  for j in dmy mdy
  do
    perl -pe 's/xeq 01/\nxeq 01/; s/  "/\n"/;' testcf$i$j.txt > testcf.txt
    ./hp41uc /t=testcf.txt /r=testcf.raw
    mv TESTCF.RAW TESTCF$i$j.RAW
  done
done
```

If you prefer the MS-DOS shell syntax, or if Cygwin or Perl is not
installed, take this script as an inspiration
to write your own script with your preferred syntax.

And then, switch to v41 and execute:


<pre>
GTO ..
File -&gt; Get User Code...
</pre>


## nonpareil

For the "nonpareil" simulator, the only way that I know to
enter these programs consists in typing them from the beginning
to the end. If I learn of another way, I will let you know
in a new version.


## The programs

## `check`

This subprogram receives a Gregorian date in register X and the
expected value for the French Revolutionary date in alpha register.
It computes the French Revolutionary date and compares it with
the expected value. If they differ, it retrieves the input parameters,
emits a tone and stops.


```
lbl 01
sto 10
asto 11
ashf
asto 12
xeq "CALFR"
asto x
rcl 11
x#y?
gto 02
ashf
asto x
rcl 12
x=y?
rtn
lbl 02
cla
arcl 11
arcl 12
rcl 10
tone 0
sf 00
prompt
rtn
```

## `end` - The end of the programs

```
"REUSSI"
fs? 00
"ECHEC"
prompt
gto 00
rtn
<<<<check>>>>
end
```

## `testcf1dmy`

```
lbl "TESTCF1"
lbl 00
cf 00
22.091792   "1 VND 1"      xeq 01
23.101793   "2 BRU 2"      xeq 01
27.071794   "9 THE 2"      xeq 01
23.111794   "3 FRI 3"      xeq 01
 5.101795  "13 VND 4"      xeq 01
25.121795   "4 NIV 4"      xeq 01
24.011797   "5 PLU 5"      xeq 01
24.021798   "6 VNT 6"      xeq 01
 9.111799  "18 BRU 8"      xeq 01
29.031801   "8 GER 9"      xeq 01
30.041804  "10 FLO 12"     xeq 01
 1.061807  "12 PRA 15"     xeq 01
 3.071810  "14 MES 18"     xeq 01
 4.081813  "16 THE 21"     xeq 01
 4.091816  "18 FRU 24"     xeq 01
 1.012000  "12 NIV 208"    xeq 01
11.052001  "22 FLO 209"    xeq 01
22.091792   "1 VND 1"      xeq 01
21.091793   "5 S-C 1"      xeq 01
22.091793   "1 VND 2"      xeq 01
<<<<end>>>>
```

## `testcf2dmy`

```
lbl "TESTCF2"
lbl 00
cf 00
21.091794   "5 S-C 2"      xeq 01
22.091794   "1 VND 3"      xeq 01
22.091795   "6 S-C 3"      xeq 01
23.091795   "1 VND 4"      xeq 01
21.091796   "5 S-C 4"      xeq 01
22.091796   "1 VND 5"      xeq 01
21.091797   "5 S-C 5"      xeq 01
22.091797   "1 VND 6"      xeq 01
22.091799   "6 S-C 7"      xeq 01
23.091799   "1 VND 8"      xeq 01
22.091800   "5 S-C 8"      xeq 01
23.091800   "1 VND 9"      xeq 01
22.091801   "5 S-C 9"      xeq 01
23.091801   "1 VND 10"     xeq 01
22.091823   "5 S-C 31"     xeq 01
23.091823   "1 VND 32"     xeq 01
22.091824   "6 S-C 32"     xeq 01
23.091824   "1 VND 33"     xeq 01
22.091825   "5 S-C 33"     xeq 01
23.091825   "1 VND 34"     xeq 01
<<<<end>>>>
```

## `testcf3dmy`

```
lbl "TESTCF3"
lbl 00
cf 00
21.091892   "5 S-C 100"    xeq 01
22.091892   "1 VND 101"    xeq 01
22.091900   "6 S-C 108"    xeq 01
23.091900   "1 VND 109"    xeq 01
21.091992   "5 S-C 200"    xeq 01
22.091992   "1 VND 201"    xeq 01
21.092000   "6 S-C 208"    xeq 01
22.092000   "1 VND 209"    xeq 01
20.092092   "5 S-C 300"    xeq 01
21.092092   "1 VND 301"    xeq 01
21.092100   "6 S-C 308"    xeq 01
22.092100   "1 VND 309"    xeq 01
21.092192   "6 S-C 400"    xeq 01
22.092192   "1 VND 401"    xeq 01
21.092193   "5 S-C 401"    xeq 01
22.092199   "1 VND 408"    xeq 01
22.092200   "6 S-C 408"    xeq 01
23.092791   "1 VND 1000"   xeq 01
22.092792   "1 VND 1001"   xeq 01
 1.013000  "12 NIV 1208"   xeq 01
<<<<end>>>>
```

## `testcf4dmy`

```
lbl "TESTCF4"
lbl 00
cf 00
 1.013001  "11 NIV 1209"   xeq 01
22.093791   "1 VND 2000"   xeq 01
22.093792   "1 VND 2001"   xeq 01
 1.014000  "12 NIV 2208"   xeq 01
 1.014001  "12 NIV 2209"   xeq 01
10.094320  "24 FRU 2528"   xeq 01
<<<<end>>>>
```

## `testcf5dmy`

```
lbl "TESTCF5"
lbl 00
cf 00
11.094320  "25 FRU 2528"   xeq 01
23.094791   "1 VND 3000"   xeq 01
22.094792   "1 VND 3001"   xeq 01
 1.015000  "12 NIV 3208"   xeq 01
 1.015001  "11 NIV 3209"   xeq 01
22.095791   "1 VND 4000"   xeq 01
21.095792   "1 VND 4001"   xeq 01
 1.016000  "13 NIV 4208"   xeq 01
 1.016001  "13 NIV 4209"   xeq 01
22.096791   "1 VND 5000"   xeq 01
21.096792   "1 VND 5001"   xeq 01
21.097791   "1 VND 6000"   xeq 01
21.097792   "1 VND 6001"   xeq 01
<<<<end>>>>
```

## `testcf1mdy`

```
lbl "TESTCF1"
lbl 00
cf 00
 9.221792   "1 VND 1"      xeq 01
10.231793   "2 BRU 2"      xeq 01
 7.271794   "9 THE 2"      xeq 01
11.231794   "3 FRI 3"      xeq 01
10.051795  "13 VND 4"      xeq 01
12.251795   "4 NIV 4"      xeq 01
 1.241797   "5 PLU 5"      xeq 01
 2.241798   "6 VNT 6"      xeq 01
11.091799  "18 BRU 8"      xeq 01
 3.291801   "8 GER 9"      xeq 01
 4.301804  "10 FLO 12"     xeq 01
 6.011807  "12 PRA 15"     xeq 01
 7.031810  "14 MES 18"     xeq 01
 8.041813  "16 THE 21"     xeq 01
 9.041816  "18 FRU 24"     xeq 01
 1.012000  "12 NIV 208"    xeq 01
 5.112001  "22 FLO 209"    xeq 01
 9.221792   "1 VND 1"      xeq 01
 9.211793   "5 S-C 1"      xeq 01
 9.221793   "1 VND 2"      xeq 01
<<<<end>>>>
```

## `testcf2mdy`

```
lbl "TESTCF2"
lbl 00
cf 00
 9.211794   "5 S-C 2"      xeq 01
 9.221794   "1 VND 3"      xeq 01
 9.221795   "6 S-C 3"      xeq 01
 9.231795   "1 VND 4"      xeq 01
 9.211796   "5 S-C 4"      xeq 01
 9.221796   "1 VND 5"      xeq 01
 9.211797   "5 S-C 5"      xeq 01
 9.221797   "1 VND 6"      xeq 01
 9.221799   "6 S-C 7"      xeq 01
 9.231799   "1 VND 8"      xeq 01
 9.221800   "5 S-C 8"      xeq 01
 9.231800   "1 VND 9"      xeq 01
 9.221801   "5 S-C 9"      xeq 01
 9.231801   "1 VND 10"     xeq 01
 9.221823   "5 S-C 31"     xeq 01
 9.231823   "1 VND 32"     xeq 01
 9.221824   "6 S-C 32"     xeq 01
 9.231824   "1 VND 33"     xeq 01
 9.221825   "5 S-C 33"     xeq 01
 9.231825   "1 VND 34"     xeq 01
<<<<end>>>>
```

## `testcf3mdy`

```
lbl "TESTCF3"
lbl 00
cf 00
 9.211892   "5 S-C 100"    xeq 01
 9.221892   "1 VND 101"    xeq 01
 9.221900   "6 S-C 108"    xeq 01
 9.231900   "1 VND 109"    xeq 01
 9.211992   "5 S-C 200"    xeq 01
 9.221992   "1 VND 201"    xeq 01
 9.212000   "6 S-C 208"    xeq 01
 9.222000   "1 VND 209"    xeq 01
 9.202092   "5 S-C 300"    xeq 01
 9.212092   "1 VND 301"    xeq 01
 9.212100   "6 S-C 308"    xeq 01
 9.222100   "1 VND 309"    xeq 01
 9.212192   "6 S-C 400"    xeq 01
 9.222192   "1 VND 401"    xeq 01
 9.212193   "5 S-C 401"    xeq 01
 9.222199   "1 VND 408"    xeq 01
 9.222200   "6 S-C 408"    xeq 01
 9.232791   "1 VND 1000"   xeq 01
 9.222792   "1 VND 1001"   xeq 01
 1.013000  "12 NIV 1208"   xeq 01
<<<<end>>>>
```

## `testcf4mdy`

```
lbl "TESTCF4"
lbl 00
cf 00
 1.013001  "11 NIV 1209"   xeq 01
 9.223791   "1 VND 2000"   xeq 01
 9.223792   "1 VND 2001"   xeq 01
 1.014000  "12 NIV 2208"   xeq 01
 1.014001  "12 NIV 2209"   xeq 01
 9.104320  "24 FRU 2528"   xeq 01
<<<<end>>>>
```

## `testcf5mdy`

```
lbl "TESTCF5"
lbl 00
cf 00
 9.114320  "25 FRU 2528"   xeq 01
 9.234791   "1 VND 3000"   xeq 01
 9.224792   "1 VND 3001"   xeq 01
 1.015000  "12 NIV 3208"   xeq 01
 1.015001  "11 NIV 3209"   xeq 01
 9.225791   "1 VND 4000"   xeq 01
 9.215792   "1 VND 4001"   xeq 01
 1.016000  "13 NIV 4208"   xeq 01
 1.016001  "13 NIV 4209"   xeq 01
 9.226791   "1 VND 5000"   xeq 01
 9.216792   "1 VND 5001"   xeq 01
 9.217791   "1 VND 6000"   xeq 01
 9.217792   "1 VND 6001"   xeq 01
<<<<end>>>>
```

## License

This code is published under the same terms as Perl: GPL and Artistic License.
See the <tt>LICENSE</tt> file provided in the package.


