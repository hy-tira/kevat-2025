---
title: Hajautustaulu
permalink: /hajautustaulu
hide: true
---
    
# Hajautustaulu

_Hajautustaulu_ (_hash table_) on tietorakenne, jonka avulla voidaan pitää yllä tehokkaasti alkioiden joukkoa. Esimerkiksi Pythonin tietorakenteet `set` ja `dict` on toteutettu hajautustaulun avulla.

Hajautustaulussa on $$N$$ kohtaa, joilla on indeksit $$0,1,\dots,N-1$$. Jokaisella alkiolla on hajautustaulussa tietty sijainti, joka lasketaan alkion arvon perusteella.

_Hajautusfunktio_ (_hash function_) määrittää alkion sijainnin hajautustaulussa. Funktiolle annetaan alkio $$x$$ ja se palauttaa kokonaisluvun $$h(x)$$ eli _hajautusarvon_ (_hash value_). Alkion sijainti hajautustaulussa on $$h(x) \bmod N$$ eli $$h(x)$$:n jakojäännös $$N$$:llä.

Kun alkio $$x$$ lisätään hajautustauluun, se lisätään kohtaan $$h(x) \bmod N$$. Vastaavasti kun halutaan tarkastaa, onko hajautustaulussa alkiota $$x$$, sitä etsitään hajautustaulun kohdasta $$h(x) \bmod N$$.

## Esimerkki

Tarkastellaan esimerkkiä, jossa $$N=10$$ ja $$h(x)=7x$$. Tallennetaan hajautustauluun joukon $$\{2,4,5,9,18,30\}$$ sisältö. Alkioille saadaan seuraavat hajautusarvot:

Alkio | Hajautusarvo
-- | --
$$2$$ | $$7 \cdot 2 \bmod 10 = 4$$
$$4$$ | $$7 \cdot 4 \bmod 10 = 8$$
$$5$$ | $$7 \cdot 5 \bmod 10 = 5$$
$$9$$ | $$7 \cdot 9 \bmod 10 = 3$$
$$18$$ | $$7 \cdot 18 \bmod 10 = 6$$
$$30$$ | $$7 \cdot 30 \bmod 10 = 0$$

Tämän perusteella alkiot sijoitetaan hajautustauluun seuraavasti:

Kohta | $$0$$ | $$1$$ | $$2$$ | $$3$$ | $$4$$ | $$5$$ | $$6$$ | $$7$$ | $$8$$ | $$9$$
Alkiot | $$30$$ | -- | -- | $$9$$ | $$2$$ | $$5$$ | $$18$$ | -- | $$4$$ | --

Kun halutaan tarkastaa, onko hajautustaulussa alkiota $$x$$, sitä etsitään hajautustaulun kohdasta $$7x \bmod 10$$. Esimerkiksi kun alkio on $$4$$, sitä etsitään hajautustaulun kohdasta $$7 \cdot 4 \bmod 10 = 8$$.

## Törmäykset

_Törmäys_ (_collision_) on tilanne, jossa kahdella alkiolla on sama sijainti hajautustaulussa. Tällaiseen tilanteeseen tulee varautua hajautustaulun toteutuksessa, koska hajautustaulun koko $$N$$ on yleensä aina pienempi kuin mahdollisten alkioiden määrä.

Esimerkiksi jos äskeiseen hajautustauluun sijoitetaan alkiot $$4$$ ja $$14$$, kummankin alkion sijainniksi tulee $$8$$, koska $$7 \cdot 4 \bmod 10 = 7 \cdot 14 \bmod 10 = 8$$.

Törmäysten käsittelyyn on kaksi tavallista tapaa:

* _Ketjuhajautus_ (_chaining_): Jokaisessa hajautustaulun kohdassa on lista, joka sisältää kaikki siihen kohtaan kuuluvat alkiot.

* _Avoin hajautus_ (_open addressing_): Jokaisessa hajautustaulun kohdassa voi olla enintään yksi alkio. Alkion $$x$$ paikka etsitään funktiolla $$p(x,i)$$ kokeilemalla arvoja $$i=0,1,2,\dots$$ kunnes löytyy vapaa paikka.

## Tehokkuus

Hajautustaulu toimii tehokkaasti, jos alkiot sijoittuvat tasaisesti eri puolille hajautustaulua. Tähän vaikuttavat hajautustaulun koko ja hajautusfunktion valinta.

Ketjuhajautuksessa hajautustaulu on tehokas, jos jokaisessa hajautustaulun kohdassa on pieni määrä alkioita, jolloin listat ovat lyhyitä ja niiden käsittely on tehokasta.

Avoimessa hajautuksessa hajautustaulu on tehokas, jos riittää pieni määrä kokeiluja alkion paikan löytämiseen eli funktiota $$p(x,i)$$ kutsutaan pieni määrä kertoja.

Jos alkiot sijoittuvat tasaisesti hajautustauluun ja hajautustaulu on riittävän suuri, hajautustaulun operaatiot (lisäys, haku, poisto) vievät aikaa $$O(1)$$. Käytännössä yleensä on aina näin, minkä takia voidaan olettaa, että hajautustaulun operaatiot vievät aikaa $$O(1)$$.

Kuitenkin pahimmassa tapauksessa hajautustaulu voi toimia hitaasti ja operaatiot vievät aikaa $$O(n)$$. Näin käy esimerkiksi ketjuhajautuksessa silloin, kun jokainen alkio saa saman hajautusarvon.
