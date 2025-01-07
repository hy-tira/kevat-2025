---
title: Linkitetty lista
permalink: /linkitetty-lista
hide: true
---

# Linkitetty lista

_Linkitetty lista_ (_linked list_) on tietorakenne, jossa listan alkiot tallennetaan muistiin niin, että jokaisessa alkiossa on viittaus edelliseen tai seuraavaan alkioon. Toisin kuin tavallisessa listassa, alkioiden ei tarvitse olla peräkkäin muistissa.

Tarkastellaan esimerkkinä listan $$[1,2,3]$$ tallentamista tavallisena listana ja linkitettynä listana. Tavallisena listana tilanne voi näyttää tältä:

100 | 101 | 102 | 103 | 104 | 105 | 106 | 107 | 108 | 109 | 110 | 111
1 | 2 | 3 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0

Tässä listan alkiot on tallennettu muistiin alkaen kohdasta 100. Alkiot ovat peräkkäin muistissa ja lisäykset ja poistot ovat tehokkaita vain listan lopussa.

Linkitettynä listana tilanne voi puolestaan näyttää seuraavalta:

100 | 101 | 102 | 103 | 104 | 105 | 106 | 107 | 108 | 109 | 110 | 111
1 | 0 | 106 | 3 | 106 | 0 | 2 | 100 | 103 | 0 | 0 | 0

Tässä listan alkiot ovat kohdissa 100, 106 ja 103. Jokaisessa kohdassa on kolme tietoa: alkion sisältö, viittaus edelliseen alkioon ja viittaus seuraavaan alkioon. Esimerkiksi kohdassa 106 on arvot 2, 100, 103, koska alkion sisältö on 2, edellinen alkio on kohdassa 100 ja seuraava alkio on kohdassa 103. Viittaus 0 tarkoittaa, että alkiolla ei ole edellistä tai seuraavaa alkiota.

Linkitetyn listan etuna on, että alkioita voi lisätä ja poistaa joustavasti, koska alkiot voivat olla missä tahansa kohdissa muistissa. Esimerkiksi jos yllä olevassa tilanteessa listan alkuun halutaan lisätä uusi alkio, se voidaan lisätä muistiin kohtaan 109:

100 | 101 | 102 | 103 | 104 | 105 | 106 | 107 | 108 | 109 | 110 | 111
1 | 109 | 106 | 3 | 106 | 0 | 2 | 100 | 103 | 4 | 0 | 100

Tämä vastaa listaa $$[4,1,3,2]$$, jonka aloituskohta muistissa on 109.

Linkitetyn listan heikkoutena on, että ei ole tehokasta tapaa laskea, missä kohdassa muistia mikäkin alkio sijaitsee. Jos halutaan päästä käsiksi tietyssä kohdassa olevaan alkioon, täytyy seurata viittauksia listan alusta tai lopusta. Tämän vuoksi indeksointi vie aikaa $$O(n)$$, jos alkio sijaitsee listan keskellä.
