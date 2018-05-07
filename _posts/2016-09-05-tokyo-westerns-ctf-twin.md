---
layout: post
title: Tokyo Westerns / MMA 2nd 2016 - Twin Primes
categories: [ctf]
tags: [writeups, infosec, ctf, crypto]
fullview: false
comments: true
---
>Decrypt it.
>[twin-primes.7z](../assets/media/twin-primes.7z)

An easy crypto chall, useful for better understanding PyCrypto and RSA. The encrypting algorithm given uses RSA two times, providing also the public key components.

Our solution was very straightforward: with the value of *n*, we tried to crack the value of the prime number factors (*n=p**q*).

How? [factodb](http://factordb.com/)! Using previously calculated tables we easily obtain *p* and *q*. Now all we had to do was calculate *d* and decrypt it normally.

I wrote a simple and ugly script for this:

~~~~
from Crypto.Util.number import *
import Crypto.PublicKey.RSA as RSA
import os

e = long(65537)
p2 = 109839168287920364771652233739542245893972429420400471787477887103169099491804762856071669374751286279860451783039232642710981517010937585802203565874477414469934412741906018847402147404957765188018616912003220542453809516059524224015255036266232001320821428611494617812180060212800300789614856560253120304703
q2 = 176645945799298135110721766377313792982812334295271987596634864064777954683139799946630491521527848390622912482826980130051166690303653228530141163053890146954290070312482492552495214917023922382112893625586133272913759418717134953590760109002220865007673751773346439753002517112721944238066505389966935631253
p1 = 109839168287920364771652233739542245893972429420400471787477887103169099491804762856071669374751286279860451783039232642710981517010937585802203565874477414469934412741906018847402147404957765188018616912003220542453809516059524224015255036266232001320821428611494617812180060212800300789614856560253120304701
q1 = 176645945799298135110721766377313792982812334295271987596634864064777954683139799946630491521527848390622912482826980130051166690303653228530141163053890146954290070312482492552495214917023922382112893625586133272913759418717134953590760109002220865007673751773346439753002517112721944238066505389966935631251

n1 = p1*q1
n2 = p2*q2

d1 = inverse(e, (p1-1)*(q1-1))
d2 = inverse(e, (p2-1)*(q2-1))

#print("p2: %d \nq2: %d	\nd2:%d" % (p2, q2, d2))
#print("p1: %d \nq2: %d	\nd2:%d" % (p1, q1, d1))

c = long(7991219189591014572196623817385737879027208108469800802629706564258508626010674513875496029177290575819650366802730803283761137036255380767766538866086463895539973594615882321974738140931689333873106124459849322556754579010062541988138211176574621668101228531769828358289973150393343109948611583609219420213530834364837438730411379305046156670015024547263019932288989808228091601206948741304222197779808592738075111024678982273856922586615415238555211148847427589678238745186253649783665607928382002868111278077054871294837923189536714235044041993541158402943372188779797996711792610439969105993917373651847337638929)

pkey2 = RSA.construct((n2, e, d2, p2, q2))
pkey1 = RSA.construct((n1, e, d1, p1, q1))

m2 = pkey2.decrypt(c)
m1 = pkey1.decrypt(m2)

print("flag: %s" % (long_to_bytes(m1)))
~~~~

FLAG 1: TWCTF{3102628d7059fa267365f8c37a0e56cf7e0797ef}