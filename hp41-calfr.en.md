-*- encoding: utf-8; indent-tabs-mode: nil -*-

# French Revolutionary Calendar for HP-41

## Introduction

This program for HP-41CV or HP-41CX allows you to convert dates
from the Gregorian calendar to the French Revolutionary
Calendar. It is not limited to the historical use period
of the French Revolutionary calendar, you can convert
later dates, in the whole XIXth, XXth and XXIst centuries.


## Instructions for Use

Enter the date in <tt>dd.mmyyyy</tt> or <tt>mm.ddyyyy</tt> format into the X register
(on the HP-41CV, use the format corresponding to the program variant you chose;
on the HP-41CX, used the format currently defined by
the last <tt>DMY</tt> or <tt>MDY</tt> operation),
go to the beginning of the program (<tt>RTN</tt>) then
run it (<tt>R/S</tt>). You can run the program with
<tt>XEQ alpha CALFR alpha</tt>.

The result is in the alpha register, in <tt>DD MON YYYY</tt>
format, with <tt>MON</tt> a 3-letter abbreviation of the month.

For example, if you enter <tt>9.111799</tt> (9th November 1799), the HP-41
will display <tt>18 BRU 8</tt> (18 Brumaire VIII).

On the HP-41CX, the input date must be in the interval from 22nd September 1792 (first day
of the French Revolutionary calendar) to 10th September 4320 (highest day
allowed by the HP-41CX).


The program allows HP-41CV users to use the <tt>DDAYS</tt> function. Compared with the
version built in the HP-41CX, there are three drawbacks: the increased size of the
program, the use of R00 to R03 registers,
and the fact that you cannot switch between DD.MMYYYY and MM.DDYYYY formats at will.
The good point is that you can use dates beyond 10th September 4320.


## Installation

### Prerequisite

This program requires a HP-41CV or HP-41CX.

The program uses the memory registers 00 to 06.

There is no synthetic programming in the CX version, but the
CV versions include a <tt>rcl&nbsp;d</tt> and a <tt>sto&nbsp;d</tt>
to replace the missing <tt>rclflag</tt> and <tt>stoflag</tt> instructions.


### File Selection

If you have a HP-41CX and if you do not care about dates after
10 september 4320, use the program from the <tt>calfr-cx</tt>
file. This program uses the <tt>DDAYS</tt> function from the
TIME module embedded in the HP-41CX.

If you have a HP-41CV without a TIME module, or if
you really care about dates after 10 september 4320, then
use either the <tt>calfr-cv-dmy</tt> file or the <tt>calfr-cv-(mdy</tt>
file. Choose the one corresponding to your preferred date format,
<tt>DD.MMYYYY</tt> or <tt>MM.DDYYYY</tt>.
These files contain a <tt>DDAYS</tt> program, which replaces
the homonymous function from the TIME module.
Unfortunately, this program ca deal with only one date format.

If you have a HP-41CX and if you use the <tt>calfr-cx</tt> file,
you need not bother about date formats. The program can deal with
both of them.


### Conventions

The "append" string (symbol "<tt>|-</tt>") is represented by
the notation <tt>&gt;"string"</tt>. The "greater than" sign
outside the quotes is meant to evoke the "<tt>|-</tt>" symbol
inside the string. This is one of the notations used by
<a href="http://www.hpmuseum.org/software/41uc.htm#rules"><tt>hp41uc</tt></a>.


## Real HP-41

The only way to enter this program into a real-world HP-41
is by typing it. Although, if you have a disk drive that
can be used with HP-IL or a barcode reader, you can have a
look at the
<a href="http://www.hpmuseum.org/software/41uc.htm#wand">barcode generator</a>,
the <a href="http://www.hpmuseum.org/software/41uc.htm#sdt">shuttle disk method</a>
or the <a href="http://www.hpmuseum.org/software/41uc.htm#dlt">direct link technique</a>
in the documentation of the <a href="http://www.hpmuseum.org/software/41uc.htm">hp41uc</a> tool.


## v41

For the v41 emulator, use the
<a href="http://www.hpmuseum.org/software/41uc.htm">hp41uc</a>
tool to convert the text file into raw format. Just type:


<pre>
hp41uc /t=calfr-cx /r=calfr-cx.raw /n
</pre>


And it works even with the Unix-like shell from Cygwin,
using slashes for the parameters, even if you are used to type dashes.
And then, load the program into the v41 emulator with File -&gt; Get User Code...


## nonpareil

For the "nonpareil" simulator, the only way that I know to
enter this program consists in typing it from the beginning
to the end. If I learn of another way, I will let you know
in a new version.


## The program

## `calfr1`

```
lbl "CALFR"
lbl 30
<<<<frrdn>>>>
365
/
int
2
+
sto 01
lbl 00
1
st- 01
rcl 01
xeq 20
rcl 01
365
*
lastx
-
+
rcl 00
x<>y
x>y?
gto 00
-
sto 03
30
/
int
sto 02
30
*
st- 03
1
st+ 02
st+ 03
<<<<display>>>>
clx
rtn
gto 30
<<<<frbis>>>>
<<<<months>>>>
```

## `frrdn` - Computing the <i lang='la'>Franciscus Revolutionarius Rata Die</i>

Usually, the programs converting from a calendar to another use
some intermediary calendar which ignores years, months and weeks
and identifies the various days by the number of days elapsed since
a given epoch. There are the Julian Day Number, the Modifier Julian
Day Number and the <i lang='la'>Rata Die</i>, which counts the days
since the epoch of the proleptic Gregorian Calendar, that is,
1 January of year 1. My program uses a variant,
<i lang='la'>Franciscus Revolutionarius Rata Die</i> or FRRD,
which counts the days since the epoch of the French Revolutionary Calendar,
1 Vendémiaire I or 22 September 1792.
This day has number 0 in this system, so the program will be simpler.

About the way the number of days is computed on the HP-41CX: the program computes
the number of days between the given date and September 9th, 1792,
then shifts the result by 13 days. Why? Because when doing so, the
program does not check flag 31 and so does not bother whether
the current mode is <tt>DMY</tt> or <tt>MDY</tt>.


```
:::: calfr-cx ::::
9,091792
x<>y
ddays
13
-
sto 00
```

```
:::: calfr-cv-dmy, calfr-cv-mdy ::::
xeq 21
654843
-
sto 00
```

## `display` - displaying the date

Although the date is displayed with the day-month-year
format, the program begins with converting the month
from numeric (in register 02) to alphabetic (alpha register).
This is acheived with an indirect XEQ, using labels 01
to 13 (12 for Fructidor as "FRU" and 13 for the 5 or 6
additional days, named <i lang='fr'>sans-culottides</i>
and abbreviated as "S-C"). Then the 3-char abbreviation
is stored into register Y and the program displays
the day, the month and the year separated by whitespace.

Of course, you need a few program steps, therefore a few
bytes, to store the month abbreviation into register Y
and then to retrieve it, but this allows to write
the month abbreviations in labels 01 to 13 without
the "append" character, which saves 13 bytes.


```
xeq ind 02
```

```
:::: calfr-cx ::::
rclflag
```

```
:::: calfr-cv-dmy, calfr-cv-mdy ::::
rcl d
```

```
fix 0
cf 29
asto y
cla
arcl 03
>" "
arcl y
>" "
arcl 01
```

```
:::: calfr-cx ::::
stoflag
```

```
:::: calfr-cv-dmy, calfr-cv-mdy ::::
sto d
```

```
aview
```

## `frbis` - Number of leap years before year N

This subroutine gives the number of leap years preceding
the current year (transmitted in register X). It allows refining
the date computation, initialised with a computation using only
365-day years.

Until year XX, the leap year rule is an astronomical rule,
considering the 1st Vendémiaire should happen on the
fall equinox. Then from year XX on, there would have
been an arithmetical rule, similar to the Gregorian Calendar
rule, introduced by Gilbert Romme's reform.


See below the comparison between both rules for the beginning
of the Republican Era. The two "arithmetic" columns simulate
the hypothesis of applying the arithmetic rule from the beginning
of the era. The "Astronomical" columns show the actual case,
with the astronomical rule at first and the arithmetic rule
after. The columns "Leap?" indicates leap years with an "X".
The columns "frbis" show the result of the computation.


<table border='1'>
<tr align='center'><td>     </td><td colspan='2'> Arithmetic </td><td colspan='2'>Astronomical</td></tr>
<tr align='center'><td>Year </td><td>Leap?</td><td>frbis </td><td>Leap?</td><td>frbis </td></tr>


<tr align='center'><td>  1  </td><td>&nbsp;</td><td>  0   </td><td>&nbsp;</td><td>  0   </td></tr>
<tr align='center'><td>  2  </td><td>&nbsp;</td><td>  0   </td><td>&nbsp;</td><td>  0   </td></tr>
<tr align='center'><td>  3  </td><td>&nbsp;</td><td>  0   </td><td>  x   </td><td>  0   </td></tr>
<tr align='center'><td>  4  </td><td>  x   </td><td>  0   </td><td>&nbsp;</td><td>  1   </td></tr>
<tr align='center'><td>  5  </td><td>&nbsp;</td><td>  1   </td><td>&nbsp;</td><td>  1   </td></tr>
<tr align='center'><td>  6  </td><td>&nbsp;</td><td>  1   </td><td>&nbsp;</td><td>  1   </td></tr>
<tr align='center'><td>  7  </td><td>&nbsp;</td><td>  1   </td><td>  x   </td><td>  1   </td></tr>
<tr align='center'><td>  8  </td><td>  x   </td><td>  1   </td><td>&nbsp;</td><td>  2   </td></tr>
<tr align='center'><td>  9  </td><td>&nbsp;</td><td>  2   </td><td>&nbsp;</td><td>  2   </td></tr>
<tr align='center'><td> 10  </td><td>&nbsp;</td><td>  2   </td><td>&nbsp;</td><td>  2   </td></tr>
<tr align='center'><td> 11  </td><td>&nbsp;</td><td>  2   </td><td>  x   </td><td>  2   </td></tr>
<tr align='center'><td> 12  </td><td>  x   </td><td>  2   </td><td>&nbsp;</td><td>  3   </td></tr>
<tr align='center'><td> 13  </td><td>&nbsp;</td><td>  3   </td><td>&nbsp;</td><td>  3   </td></tr>
<tr align='center'><td> 14  </td><td>&nbsp;</td><td>  3   </td><td>&nbsp;</td><td>  3   </td></tr>
<tr align='center'><td> 15  </td><td>&nbsp;</td><td>  3   </td><td>  x   </td><td>  3   </td></tr>
<tr align='center'><td> 16  </td><td>  x   </td><td>  3   </td><td>&nbsp;</td><td>  4   </td></tr>
<tr align='center'><td> 17  </td><td>&nbsp;</td><td>  4   </td><td>&nbsp;</td><td>  4   </td></tr>
<tr align='center'><td> 18  </td><td>&nbsp;</td><td>  4   </td><td>&nbsp;</td><td>  4   </td></tr>
<tr align='center'><td> 19  </td><td>&nbsp;</td><td>  4   </td><td>&nbsp;</td><td>  4   </td></tr>
<tr align='center'><td> 20  </td><td>  x   </td><td>  4   </td><td>  x   </td><td>  4   </td></tr>
<tr align='center'><td> 21  </td><td>&nbsp;</td><td>  5   </td><td>&nbsp;</td><td>  5   </td></tr>
<tr align='center'><td> 22  </td><td>&nbsp;</td><td>  5   </td><td>&nbsp;</td><td>  5   </td></tr>
<tr align='center'><td> 23  </td><td>&nbsp;</td><td>  5   </td><td>&nbsp;</td><td>  5   </td></tr>
<tr align='center'><td> 24  </td><td>  x   </td><td>  5   </td><td>  x   </td><td>  5   </td></tr>
<tr align='center'><td> 25  </td><td>&nbsp;</td><td>  6   </td><td>&nbsp;</td><td>  6   </td></tr>
</table>


Actually, the two rules differ only for years IV, VIII, XII
and XVI. So the subroutine applies the arithmetical rule
starting from year I
and then checks the year, tweaking the result is the year
is one of the four exceptions. Remember that the subroutine begins
with subtracting 1 from the year, therefore the values to
check are 3, 7, 11 and 15.


```
lbl 20
1
-
sto 05
4
/
int
rcl 05
100
/
int
-
rcl 05
400
/
int
+
rcl 05
4000
/
int
-
sto 06
rcl 05
15
x<y?
gto 22
x=y?
gto 14
rdn
11
x=y?
gto 14
rdn
7
x=y?
gto 14
rdn
3
x=y?
gto 14
lbl 22
rcl 06
rtn
lbl 14
rcl 06
1
+
rtn
```

## `months` - Month abbreviations

This section gives the abbreviated month from the
French Revolutionary calendar. One of these labels
is called by <tt>XEQ IND 02</tt>, because the R02
register contains the numeric month.


```
lbl 01
"VND"
rtn
lbl 02
"BRU"
rtn
lbl 03
"FRI"
rtn
lbl 04
"NIV"
rtn
lbl 05
"PLU"
rtn
lbl 06
"VNT"
rtn
lbl 07
"GER"
rtn
lbl 08
"FLO"
rtn
lbl 09
"PRA"
rtn
lbl 10
"MES"
rtn
lbl 11
"THE"
rtn
lbl 12
"FRU"
rtn
lbl 13
"S-C"
rtn
```

## `ddays` - Computing the number of days between two dates

```
lbl "DDAYS"
sto 00
rdn
xeq 21
x<> 00
xeq 21
rcl 00
-
rtn
lbl 21
int
```

```
:::: calfr-cv-dmy ::::
sto 01
```

```
:::: calfr-cv-mdy ::::
sto 02
```

```
lastX
frc
1e2
*
int
```

```
:::: calfr-cv-dmy ::::
sto 02
```

```
:::: calfr-cv-mdy ::::
sto 01
```

```
lastX
frc
1e4
*
sto 03
2
rcl 02
x>y?
gto 22
1
st- 03
12
st+ 02
lbl 22
rcl 03
365.25
*
int
rcl 03
100
/
int
-
rcl 03
400
/
int
+
rcl 02
1
+
30.6
*
int
+
rcl 01
+
rtn
```

## License

This code is published under the same terms as Perl: GPL and Artistic License.
See the <tt>LICENSE</tt> file provided in the package.


