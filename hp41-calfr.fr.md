-*- encoding: utf-8; indent-tabs-mode: nil -*-

# Calendrier républicain pour HP-41

## Introduction

Ce programme pour HP-41CV ou HP-41CX permet de convertir une date du calendrier
grégorien vers le calendrier républicain. Il n'est pas
limité à la période réelle d'utilisation du calendrier 
républicain, vous pouvez demander la conversion de toute
date des XIX<sup>e</sup>, XX<sup>e</sup> et XXI<sup>e</sup>
siècles.


## Mode d'emploi

Entrez la date au format <tt>jj.mmaaaa</tt> ou <tt>mm.jjaaaa</tt> 
(selon la variante choisie du programme pour HP-41CV ou selon
le paramétrage de la calculatrice HP-41CX) dans le registre X,
positionnez-vous au début du programme (<tt>RTN</tt>) puis 
lancez-le (<tt>R/S</tt>). Ou bien lancez-le par 
<tt>XEQ alpha CALFR alpha</tt>.

Le résultat se trouve dans le registre alpha, au format 
<tt>JJ MOIS AAAA</tt>, où <tt>MOIS</tt> est l'abréviation en trois lettres
du mois.

Par exemple, vous entrez <tt>9.111799</tt> (9 novembre 1799) et la HP-41
vous affiche <tt>18 BRU 8</tt> (18 Brumaire VIII).

Sur la HP-41CX, la date doit être comprise entre le 22 septembre 1792 (début du calendrier
républicain) et le 10 septembre 4320 (date limite imposée par la HP-41CX).


Le programme permet aux utilisateurs de HP-41CV d'utiliser la fonction <tt>DDAYS</tt>.
Par rapport à la version intrinsèque de la HP-41CX, les inconvénients sont 
la taille accrue du programme, l'utilisation
des registres R00 à R03, ainsi que le fait que le format JJ,MMAAAA ou MM,JJAAAA est figé.
L'avantage est que l'on peut utiliser des dates au-delà du 10 septembre 4320.


## Installation

### Pré-requis

Ce programme nécessite une HP-41CV ou une HP-41CX. 

Le programme utilise les mémoires 00 à 06.

Il n'y a pas d'instruction de programmation synthétique dans
la version CX, mais il y a un <tt>rcl&nbsp;d</tt> et un <tt>sto&nbsp;d</tt> dans les 
versions CV, pour compenser l'absence de <tt>rclflag</tt>
et <tt>stoflag</tt>


### Choix du fichier

Si vous disposez d'une HP-41CX et si vous ne vous intéressez
pas aux dates postérieures au 10 septembre 4320, alors
utilisez le fichier <tt>calfr-cx</tt>. Le programme de ce 
fichier utilise la fonction <tt>DDAYS</tt> du module TIME
inclus dans la HP-41CX.

Si vous possédez une HP-41CV sans module TIME, ou bien
si vous tenez absolument à convertir des dates postérieures
au 10 septembre 4320, alors utilisez l'un des fichiers
<tt>calfr-cv-dmy</tt> ou <tt>calfr-cv-mdy</tt>.
Ce fichier contient un programme <tt>DDAYS</tt> destiné
à pallier l'absence de la fonction homonyme dans la HP-41CV.
Hélas, ce programme ne sait traiter qu'un seul format de date.
Choisissez <tt>calfr-cv-dmy</tt> si vous préférez saisir
les dates au format <tt>JJ,MMAAAA</tt> et choisissez
<tt>calfr-cv-mdy</tt> si vous préférez le format <tt>MM,JJAAAA</tt>.

En revanche, le programme du fichier <tt>calfr-cx</tt> sait
traiter les deux formats, sans problème.


### Conventions d'écriture

L'ajout de chaîne (le symbole «&nbsp;<tt>|-</tt>&nbsp;») se représente
par la notation <tt>&gt;"chaine"</tt>. Le signe supérieur
à l'extérieur des guillemets est censé représenter le 
symbole «&nbsp;<tt>|-</tt>&nbsp;» qui se serait trouvé à l'intérieur
des guillemets. C'est l'une des notations utilisées
par <a href="http://www.hpmuseum.org/software/41uc.htm#rules"><tt>hp41uc</tt></a>.


## HP-41 réelle

La seule façon de rentrer ce programme consiste à le taper.
Toutefois, si vous avez une unité de disque  fonctionnant
sous HP-IL ou un lecteur de codes-barres, vous pouvez jeter
un coup d'œil au
<a href="http://www.hpmuseum.org/software/41uc.htm#wand">générateur de codes-barres</a>,
à la <a href="http://www.hpmuseum.org/software/41uc.htm#sdt">méthode des disques navettes</a>
ou à la <a href="http://www.hpmuseum.org/software/41uc.htm#dlt">technique du lien direct</a>
dans la documentation de l'outil <a href="http://www.hpmuseum.org/software/41uc.htm">hp41uc</a>.


## v41

Pour l'émulateur v41, utilisez
l'outil <a href="http://www.hpmuseum.org/software/41uc.htm">hp41uc</a>
pour convertir le fichier texte en fichier <i lang='en'>raw</i>.
Il suffit de taper&nbsp;:


<pre>
hp41uc /t=calfr-cx /r=calfr-cx.raw /n
</pre>


À noter que cela fonctionne sous le shell de Cygwin, en utilisant des
slashs pour les paramètres, même si vous êtes habitués à des tirets.
Ensuite, chargez le programme dans v41 avec File -&gt; Get User Code...


## nonpareil

Pour l'émulateur «&nbsp;nonpareil&nbsp;», la seule façon
que je connaisse consiste à tout taper, de début jusqu'à la
fin. Si je trouve mieux, je vous l'indiquerai dans une 
nouvelle version de cette distribution.


## Le programme

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

## `frrdn` - Calcul du <i lang='la'>Franciscus Revolutionarius Rata Die</i>

Habituellement, les programmes de conversion de calendrier utilisent
un calendrier intermédiaire qui ignore les années, les mois et les semaines
et qui identifie les jours par leur numéro d'ordre à partir d'une date
origine. Il y a ainsi le <i lang='en'>Julian Day Number</i>,
le <i lang='en'>Modified Julian Day Number</i> et le
<i lang='la'>Rata Die</i>, qui identifie les jours à partir de l'origine
du calendrier grégorien proleptique, le 1<sup>er</sup> janvier de l'an 1.
J'utilise ici sa variante républicaine, que j'ai appelée
<i lang='la'>Franciscus Revolutionarius Rata Die</i> ou FRRD, qui compte les
jours à partir du 1<sup>er</sup> vendémiaire de l'an I, soit le 
22 septembre 1792. Ce jour porte le numéro FRRD 0 pour des raisons
de commodité de programmation.

Au sujet du calcul sur la HP-41CX&nbsp;: au lieu de calculer le nombre de jours entre
la date donnée et le 22 septembre 1792, on calcule le nombre de jours
entre la date et le 9 septembre 1792, puis on retire les 13 jours excédentaires.
Pourquoi&nbsp;? Parce qu'ainsi, le programme sert aussi bien en mode 
<tt>DMY</tt> qu'en mode <tt>MDY</tt>, sans avoir besoin de tester le drapeau 31.


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

## `display` - affichage de la date

Bien que la date soit affichée au format
«&nbsp;jour mois an&nbsp;», on commence par afficher le mois.
Le mois figure sous forme numérique dans le registre 02
et il est converti dans le registre alpha par l'appel
indirect des labels 01 à 13 (12 = "FRU" pour Fructidor
et 13 pour les cinq ou six jours complémentaires,
«&nbsp;sans-culottides&nbsp;» abrégés en "S-C"). Ensuite, le 
mois en 3 caractères est stocké dans le registre Y
puis, dans le registre alpha, le programme affiche
dans l'ordre le jour, le mois et l'année avec des
blancs intermédiaires.

Certes, faire passer le mois par le registre Y
nécessite l'utilisation de plusieurs pas de programme,
donc de plusieurs octets, mais cela permet dans les
labels 01 à 13 de saisir l'abréviation du mois sur 
3 caractères, sans le caractère «&nbsp;append&nbsp;»,
ce qui aurait ajouté 13 octets à la taille du programme.


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

## `frbis` - Nombre d'années bissextiles avant l'année N

Ce sous-programme donne le nombre d'années bissextiles
qui ont eu lieu avant l'année courante (fournie dans le
registre X). Il permet
d'affiner le calcul de la date, par rapport à l'estimation
obtenue en prenant des années à 365&nbsp;jours.

La règle pour les années bissextiles était une règle astronomique
jusqu'en l'an XX, le premier Vendémiaire devant coïncider avec
l'équinoxe d'automne. Ensuite, la réforme de Gilbert Romme
aurait fait appliquer une règle arithmétique analogue à 
celle du calendrier grégorien.


Voici la comparaison des deux règles pour le début de l'ère
républicaine. Les deux colonnes «&nbsp;arithmétique&nbsp;»
simulent l'hypothèse où la règle arithmétique aurait été
en vigueur dès le début. Les deux colonnnes «&nbsp;astronomique&nbsp;»
présentent le cas réel&nbsp;: règle astronomique au début
et règle arithmétique ensuite. Les colonnes «&nbsp;Bis&nbsp;?&nbsp;»
indiquent par un «&nbsp;X&nbsp;» si l'année est bissextile
en fonction de la règle utilisée. Les colonnes «&nbsp;frbis&nbsp;»
indiquent le résultat du calcul de la fonction.


<table border='1'>
<tr align='center'><td>     </td><td colspan='2'>Arithmétique</td><td colspan='2'>Astronomique</td></tr>
<tr align='center'><td>Année</td><td>Bis ?</td><td>frbis </td><td>Bis ?</td><td>frbis </td></tr>


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


En fait, les deux règles n'engendrent une différence que pour
les années IV, VIII, XII et XVI. Donc le sous-programme effectue
le calcul avec la règle arithmétique dès l'an I, puis rectifie le résultat
si l'on a l'une de ces quatre années. N'oubliez pas que le sous-programme
commence par soustraire 1 à l'année, c'est pour cela qu'il teste
les valeurs 3, 7, 11 et 15.


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

## `months` - Abréviations des mois

Cette section permet d'obtenir l'abréviation du mois
du calendrier républicain. L'une de ces étiquettes
est appelée par <tt>XEQ IND 02</tt>
sachant que le registre R02 contient le mois sous forme
numérique.


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

## `ddays` - Calcul du nombre de bours entre deux dates

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

## Licence

Ce code est diffusé sous les mêmes termes que Perl, la licence GPL
et la licence artistique. Voir le fichier <tt>LICENSE</tt> (en anglais)
fourni dans le paquetage.


