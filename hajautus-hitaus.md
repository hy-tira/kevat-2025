---
title: Hajautuksen hitaus
permalink: /hajautus-hitaus
hide: true
---
    
# Hajautuksen hitaus

Hajautusta käyttävissä tietorakenteissa operaatiot toimivat _yleensä_ tehokkaasti, mutta on mahdollista, että operaatiot ovatkin hitaita törmäysten takia. Näemme seuraavaksi, miten Pythonin sanakirja muuttuu hitaaksi, kun sinne lisättävät avaimet valitaan sopivalla tavalla.

Jotta pystymme valitsemaan törmäyksiä aiheuttavat avaimet, meidän tulee tuntea sanakirjan tarkka toteutustapa. Tehokkuuteen vaikuttavia asioita ovat hajautusfunktio, hajautustaulun koko sekä törmäysten käsittelytapa. Näistä asioista on tietoa [Pythonin lähdekoodissa](https://github.com/python/cpython/blob/main/Objects/dictobject.c).

## Hajautusfunktio

Pythonin hajautusfunktio `hash` on toteutettu eri tavalla eri tietotyypeille. Tiettyä rajaa pienemmillä kokonaisluvuilla hajautusfunktio on toteutettu yksinkertaisesti niin, että kokonaisluvun hajautusarvo on sama kuin luku itse:

```console?lang=python
> hash(42)
42
> hash(123)
123
> hash(1337)
1337
```

Käytämme tässä sanakirjan avaimina pieniä kokonaislukuja, jolloin niiden hajautusarvot saadaan yllä olevalla tavalla.

## Hajautustaulun koko

Pythonin sanakirjassa hajautustaulun koko $$N$$ on alussa $$8$$. Hajautustaulu on toteutettu niin, että enintään $$2/3$$ paikoista saa olla täynnä. Jos tämä raja ylittyy alkion lisäämisen jälkeen, hajautustaulun koko kaksinkertaistetaan.

Seuraava koodi havainnollistaa asiaa:

```python
import sys

n = 1000

numbers = {}
old_size = 0

for i in range(n):
    new_size = sys.getsizeof(numbers)

    if new_size != old_size:
        print(len(numbers), sys.getsizeof(numbers))
        old_size = new_size

    numbers[i] = True
```

Koodi luo sanakirjan `numbers` ja lisää siihen alkioita yksi kerrallaan. Kun hajautustaulun koko muistissa muuttuu, koodi tulostaa alkioiden määrän ja hajautustaulun koon. Koodin tulostus on seuraava:

```
0 64
1 232
6 360
11 640
22 1176
43 2272
86 4696
171 9312
342 18520
683 36960
```

Esimerkiksi hajautustaulun koko kasvaa, kun alkioiden määräksi tulee $$6$$. Tässä tilanteessa hajautustaulun koko on ollut aiemmin $$8$$ ja siitä tulee $$16$$. Koko kasvaa, koska $$6/8$$ ylittää rajan $$2/3$$. Koko kasvaa seuraavan kerran, kun alkioiden määräksi tulee $$11$$. Tällöin kooksi tulee $$32$$, koska $$11/16$$ ylittää rajan $$2/3$$.

## Törmäysten käsittely

Seuraava koodi esittää, miten Pythonin sanakirja valitsee paikan hajautustaulussa, kun sanakirjaan tallennetaan avaimelle `key` arvo `value`.

```python
index = hash(key) % N
perturb = hash(key)
while True:
    if not table[index]:
        table[index] = (key, value)
        break
    perturb = perturb >> 5
    index = (5 * index + 1 + perturb) % N
```

Huomaa, että tämä koodi ei ole todellinen Pythonin sanakirjan taustalla oleva koodi (Python on toteutettu C-kielellä), mutta koodi esittää paikan valinnan idean.

Muuttujassa `index` on paikka hajautustaulussa ja muuttujan alkuarvo lasketaan avaimen hajautusarvon perusteella kaavalla `hash(key) % N`. Jos paikka `index` on varattu, lasketaan uusi paikka kaavalla `(5 * index + 1 + perturb) % N`. Tätä jatketaan, kunnes löytyy vapaa paikka avaimen ja arvon tallentamiseen.

Muuttujan `perturb` tavoitteena on vähentää törmäyksiä, ja se vaikuttaa paikan valintaan silmukan muutaman ensimmäisen kierroksen ajan. Muuttujan `perturb` alkuarvo on avaimen hajautusarvo, ja joka kierroksella uudeksi arvoksi tulee `perturb >> 5`. Tämä bittisiirto vastaa jakamista $$32$$:lla pyöristäen alaspäin.

## Syötteen muodostus

Nyt meillä on riittävät tiedot Pythonin sanakirjan toiminnasta, jotta voimme muodostaa syötteen, jossa sanakirja toimii hitaasti. Ideana on lisätä sanakirjan avaimia, jotka aiheuttavat törmäyksiä toisilleen ja hidastavat hajautustaulun toimintaa. Seuraava koodi muodostaa tällaisen syötteen:

```python
def find(table, key):
    N = len(table)
    index = hash(key) % N
    perturb = hash(key)
    count = 0
    while True:
        count += 1
        if not table[index]:
            return index, count
        perturb = perturb >> 5
        index = (5 * index + 1 + perturb) % N

n = 100000
chain_len = 50000
threshold = 5000

N = 2**18
table = [None] * N
keys = []

key = 1
for i in range(chain_len):
    keys.append(key)
    key = (5 * key + 1) % N

key = chain_len
while len(numbers) < n:
    index, count = find(table, key)
    if count > threshold:
        table[index] = True
        keys.append(key)
    key += 1
```

Koodi muodostaa syötteen, jossa on `n` avainta. Tätä vastaavasti valitaan hajautustaulun koko `N` niin, että enintään $$2/3$$ paikoista on käytössä. Listaan `keys` lisätään sanakirjaan tallennettavat avaimet.

Koodi lisää ensin hajautustauluun avaimia, jotka muodostavat ketjun, jonka pituus on `chain_len`. Tämän ketjun ensimmäinen avain on `1` ja muut avaimet muodostetaan kaavalla `key = (5 * key + 1) % N`. Tämän jälkeen koodi lisää loput avaimet niin, että jokaisen avaimen lisääminen aiheuttaa ainakin `threshold` askelta ketjussa kulkemista. Tämän ansiosta avainten lisääminen sanakirjaan tulee olemaan hidasta.

## Tehokkuustesti

Seuraava koodi mittaa, kauanko kestää lisätä listan `keys` avaimet sanakirjaan:

```python
import time

...

start_time = time.time()
numbers = {}
for key in keys:
    numbers[key] = True
end_time = time.time()

print(round(end_time - start_time, 2), "s")
```

Tehdään ensin testi, jossa lista `keys` sisältää $$10^5$$ satunnaisesti valittua avainta väliltä $$1 \dots 10^8$$. Lista voidaan muodostaa näin:

```python
import random

keys = random.sample(range(1, 10**8 + 1), 10**5)
```

Testikoneella tässä testissä kuluu aikaa 0.02 s. Tämä on odotettava kesto tavallisessa tilanteessa, jossa sanakirja toimii tehokkaasti.

Tehdään sitten testi, jossa lista `keys` on muodostettu aiemmalla koodilla niin, että siinä olevat avaimet aiheuttavat törmäyksiä. Nyt aikaa kuluukin 10.57 s, eli sanakirja toimii hitaasti törmäysten takia.

## Entä käytännössä?

Vaikka on mahdollista muodostaa syöte, jossa sanakirja toimii hitaasti, tällaiset syötteet ovat käytännössä hyvin harvinaisia. Niitä voi esiintyä lähinnä silloin, jos joku haluaa tahallaan saada sanakirjan toimimaan hitaasti.

Hajautuksen hitaus on kuitenkin uhka esimerkiksi web-ohjelmoinnissa, koska hyökkääjä voi koettaa lähettää sivustolle dataa, joka aiheuttaa törmäyksiä hajautuksessa. Pythonissa [tähän on varauduttu](https://github.com/python/cpython/issues/57912) niin, että funktion `hash` toiminta merkkijonoilla muuttuu aina, kun Python käynnistetään uudestaan:

```console?lang=python
$ python3
> hash("apina")
-8847049641918498300
> exit()
$ python3
> hash("apina")
5108947336973792736
> exit()
$ python3
> hash("apina")
-7637574785741815573
> exit()
```

Tämän vuoksi ei ole mahdollista muodostaa tässä esitetyllä tavalla listaa merkkijonoista, joka aiheuttaisi sanakirjan hidastumisen.
