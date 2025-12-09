-*- encoding: utf-8; indent-tabs-mode: nil -*-

# Calendrier républicain pour HP-41, tests

## Introduction

Ces programmes pour HP-41CV et HP-41CX contiennent une suite de  tests pour <tt>CALFR</tt>.


## Mode d'emploi

Si vous avez adopté la variante HP-41CX du programme de conversion,
les tests à utiliser sont les fichiers <tt>testcf</tt><var>n</var><tt>dmy.txt</tt>
et <tt>testcf</tt><var>n</var><tt>mdy.txt</tt>, avec <var>n</var> de 1 à 4.
N'utilisez pas les tests avec le numéro 5.

Si vous avez adopté l'une des variantes pour HP-41CV, utilisez les cinq fichiers
correspondant au format <tt>DMY</tt> ou <tt>MDY</tt> que vous avez installé.


Positionnez votre HP-41CX en mode <tt>DMY</tt> ou <tt>MDY</tt> selon le fichier
utilisé. Ce n'est pas nécessaire pour la HP-41CV, le format étant codé "en dur".
Lancez le programme. S'il s'arrête avec un <tt>TONE&nbsp;0</tt>,
c'est qu'il y a une erreur. La date grégorienne utilisée se trouve dans le
registre X et la date républicaine attendue dans le registre alpha.

Vous pouvez reprendre la suite des tests en appuyant sur <tt>R/S</tt>. À la fin,
le programme s'arrête en affichant selon le cas <tt>SUCCES</tt> ou <tt>ECHEC</tt>.
L'indicateur binaire 0 permet également de signaler un échec.


## Installation

### Pré-requis

Ces programmes nécessitent une HP-41CX ou une HP-41CV.
Il faut avoir installé <tt>CALFR</tt>.

Les programmes utilisent les mémoires 10 à 12, sachant que de son côté
<tt>CALFR</tt> utilise les mémoires 00 à 06.

Il n'y a pas d'instruction de programmation synthétique.

Pour des raisons de capacité de mémoire, les tests se présentent
sous la forme de plusieurs programmes au lieu d'un seul.


## HP-41 réelle

La seule façon de rentrer ces programmes consiste à les taper.
Mais le jeu en vaut-il la chandelle&nbsp;?

Toutefois, si vous avez une unité de disque fonctionnant
sous HP-IL ou un lecteur de codes-barres, vous pouvez jeter
un coup d'œil au
<a href="http://www.hpmuseum.org/software/41uc.htm#wand">générateur de codes-barres</a>,
à la <a href="http://www.hpmuseum.org/software/41uc.htm#sdt">méthode des disques navettes</a>
ou à la <a href="http://www.hpmuseum.org/software/41uc.htm#dlt">technique du lien direct</a>
dans la documentation de l'outil <a href="http://www.hpmuseum.org/software/41uc.htm">hp41uc</a>.


## v41

Pour l'émulateur v41, utilisez
l'outil <a href="http://www.hpmuseum.org/software/41uc.htm">hp41uc</a>
pour convertir les fichiers textes en fichiers <i lang='en'>raw</i>.
Vous devriez pouvoir juste taper&nbsp;:
<pre>
hp41uc /t=testcf1dmy.txt /r=testcf1dmy.raw
</pre>

Sauf que cela ne fonctionne pas pour deux raisons. D'une part,
hp41uc ne connaît que les noms de fichier 8.3. D'autre part, il refuse
les lignes comportant plusieurs instructions pour la HP-41.
La documentation assure que c'est possible en séparant les instructions
avec «&nbsp;virgule plus espace&nbsp;», mais j'ai essayé, cela ne
fonctionne pas. Donc, si vous disposez de Cygwin et de Perl, utilisez
plutôt le script suivant&nbsp;:


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

Si vous préférez la syntaxe du shell MS-DOS, ou bien s'il vous manque
Cygwin ou Perl, inspirez-vous de ce script
pour le coder avec votre syntaxe préférée.

Ensuite, sous l'émulateur v41, il ne reste plus qu'à faire&nbsp;:


<pre>
GTO ..
File → Get User Code...
</pre>


## nonpareil

Pour l'émulateur «&nbsp;nonpareil&nbsp;», la seule façon
que je connaisse consiste à tout taper, de début jusqu'à la
fin. Si je trouve mieux, je vous l'indiquerai dans une
nouvelle version de cette distribution.


## Les programmes

## `check`

Ce sous-programme reçoit une date grégorienne dans le registre
X et la valeur attendue de la date républicaine dans le registre
alpha. Il calcule la date républicaine et la compare avec la
valeur attendue. S'il y a un écart, il affiche les deux paramètres
d'entrée, émet un son et s'arrête.


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

## `end` - La fin des programmes

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

## Licence

Ce code est diffusé sous les mêmes termes que Perl, la licence GPL
et la licence artistique. Voir le fichier <tt>LICENSE</tt> (en anglais)
fourni dans le paquetage.


