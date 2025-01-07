---
title: Alkion lisääminen listaan
permalink: /lista-lisaaminen
hide: true
---
    
# Alkion lisääminen listaan

Kun listan loppuun lisätään alkio, aikaa kuluu $$O(1)$$ tai $$O(n)$$ riippuen siitä, voidaanko uusi alkio sijoittaa suoraan varatun muistialueen loppuun vai tuleeko ensin varata uusi muistialue ja siirtää listan vanha sisältö sinne.

Vaikka alkion lisääminen vie välillä aikaa $$O(n)$$, voidaan osoittaa, että aikaa kuluu _keskimäärin_ vain $$O(1)$$, jos uuden muistialueen varaaminen toteutetaan sopivalla tavalla. Yksi mahdollinen tapa on _kaksinkertaistaa_ muistialueen koko aina, kun muistia varataan lisää.

Tarkastellaan tilannetta, jossa listalle on lisätty yhteensä $$n$$ alkiota ja listalle on juuri varattu uusi muistialue. Tässä tilanteessa $$n$$ alkiota on siirretty kerran, $$n/2$$ alkiota on siirretty kahdesti, $$n/4$$ alkiota on siirretty kolmesti jne., joten yhteensä siirrettyjen alkioiden määrä on

$$n+n/2+n/4+n/8+\dots = O(n).$$

Koska listalla on yhteensä $$n$$ alkiota ja kaikkien siirtojen määrä on $$O(n)$$, kunkin alkion lisääminen on vienyt _keskimäärin_ $$O(1)$$ aikaa.

Yleisemmin jos varatun alueen koko $$c$$-kertaistetaan, siirrettyjen alkioiden määrä on

$$n+n/c+n/c^2+n/c^3+\dots = O(n),$$

kun $$c$$ on mikä tahansa vakio, joka on suurempi kuin $$1$$. Vakion $$c$$ avulla voidaan säädellä listan tehokkuuden ja muistinkäytön suhdetta. Mitä suurempi $$c$$ on, sitä harvemmin listan sisältö täytyy kopioida uuteen paikkaan muistissa mutta sitä enemmän ylimääräistä muistia listalle varataan.

## Pythonin toteutus

Seuraavan koodin avulla voidaan tutkia Pythonin listan muistinkäyttöä:

```python
import sys

n = 100

numbers = []
old_size = 0

for i in range(n):
    new_size = sys.getsizeof(numbers)

    if new_size != old_size:
        print(len(numbers), new_size)
        old_size = new_size

    numbers.append(1)
```

Koodissa käyttää funktiota `sys.getsizeof`, joka kertoo sille annetun olion muistinkäytön tavuina. Koodi aloittaa tyhjästä listasta ja lisää listalle alkion kerrallaan. Aina kun muistinkäyttö muuttuu edellisestä arvosta, koodi tulostaa listan koon ja sen muistinkäytön.

Koodin tulostus testikoneella (CPython 3.10.6) on seuraava:

```console
0 56
1 88
5 120
9 184
17 248
25 312
33 376
41 472
53 568
65 664
77 792
93 920
```

Tästä näkee, että tyhjä lista vie muistia 56 tavua ja kullekin alkiolle varataan muistia 8 tavua. Listan muistinkäyttö kasvaa tilanteissa, joissa alkioiden määräksi tulee 1, 5, 9, 17, 25, jne. Esimerkiksi kun alkioiden määräksi tulee 17, uusi muistinkäyttö on 248 tavua ja muistia varataan (248 – 56) / 8 = 24 alkiolle. Niinpä muistia tarvitaan lisää seuraavan kerran, kun alkioiden määräksi tulee 25.

Tutkimalla [CPythonin listan toteutusta](https://github.com/python/cpython/blob/0a9b339363a59be1249189c767ed6f46fd71e1c7/Objects/listobject.c#L72) selviää, että uusi muistialueen alkioiden määrä lasketaan kaavalla $$n + \lfloor n/8 \rfloor + 6$$ pyöristettynä alaspäin lähimpään 4:n moninkertaan. Esimerkiksi kun $$n=17$$, kaavasta tulee $$17+\lfloor 17/8 \rfloor + 6 = 25$$ ja tätä lähin 4:n moninkerta on $$24$$. Niinpä muistialueen koko suunnilleen $$9/8$$-kertaistuu, kun muistia varataan lisää.
