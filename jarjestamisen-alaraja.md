---
title: Järjestämisen alaraja
permalink: /jarjestamisen-alaraja
hide: true
---

# Järjestämisen alaraja

On mahdollista osoittaa, että $$O(n \log n)$$ on paras mahdollinen aikavaativuus yleiskäyttöiselle järjestämisalgoritmille. Tässä oletuksena on, että algoritmi vertailee alkioita toisiinsa ja muuttaa sen perusteella niiden järjestystä.

Tarkastellaan tilannetta, jossa algoritmille annetaan $$n$$ alkion lista, jossa jokainen luku väliltä $$1 \dots n$$ esiintyy tasan kerran. Listan alkiot voivat olla $$n!$$ eri järjestyksessä, ja kussakin tapauksessa algoritmin tulee toimia eri tavalla, jotta lista tulee järjestykseen.

Kun algoritmi vertailee kahta alkiota toisiinsa, tämä antaa algoritmille tietoa, jota se voi käyttää järjestämisessä. Tarkastellaan tilannetta, jossa $$n=3$$ ja algoritmi ei ole vielä tehnyt vertailuja. Koska algoritmilla ei ole vielä tietoa listasta, algoritmin näkökulmasta lista voi olla mikä tahansa seuraavista:

$$[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]$$

Oletetaan sitten, että algoritmi vertailee listan kahta ensimmäistä alkiota toisiinsa ja saa tietää, että ensimmäinen alkio on suurempi kuin toinen alkio. Tämän perusteella algoritmi pystyy rajaamaan tilannetta niin, että lista on jokin seuraavista:

$$[2,1,3], [3,1,2], [3,2,1]$$

Jotta algoritmi toimii oikein, sen täytyy päästä vertailuja tekemällä tilanteeseen, jossa on vain yksi mahdollinen lista ja algoritmi voi järjestää sen oikealla tavalla. Listojen määrä alussa on $$n!$$ ja jokaisen vertailun jälkeen ainakin puolet listoista voi jäädä jäljelle. Niinpä tarvitaan ainakin $$\log_2(n!)$$ vertailua, ennen kuin algoritmi on saanut varmasti riittävästi tietoa listasta. Tästä saadaan alaraja

$$\log_2(n!) = \log_2(1)+\log_2(2)+\dots+\log_2(n) \ge (n/2) \log_2(n/2)$$

eli algoritmin täytyy tehdä luokkaa $$n \log n$$ vertailua.

Huomaa, että järjestäminen voi olla mahdollista toteuttaa tehokkaammin, jos sen riittää toimia vain tietyn tyyppisillä alkioilla. Jos esimerkiksi alkiot ovat pieniä kokonaislukuja, järjestäminen on mahdollista toteuttaa ajassa $$O(n)$$. Yksi tällainen algoritmi on _laskemisjärjestäminen_ (_counting sort_).
