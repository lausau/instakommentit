---
title: "Kommenttien kerääminen Instagram-tileiltä"
author: "Laura Salonen"
date: "2/15/2021"
output: html_document
---

Tässä tekstissä käyn läpi, miten keräsimme Instagram-postauksesta kommentit tilin haltijan luvalla tekeillä olevaa artikkeliamme varten (Åberg & Salonen [in process]). 

Koska mitään APIa ei ole käytettävissä muiden kuin oman tilin tietojen keräämistä varten, pitää käyttää toisenlaisia keinoja. Yksi tapa on kerätä kommentit Instagram-postauksen nettisivun lähdekoodista. Tätä varten tarvitaan vain teksti- tai koodieditori. Koodieditoreissa (esim. Sublime, Atom, Visual Studio Code tms.) hyvänä puolena on koodien helpompi luettavuus. Itse käytin Visual Studio Codea, joten ohjeet on kirjoitettu siltä pohjalta.

### 1. Klikkaa auki kaikki kommentit
Mene selaisemassa haluamaasi Instagram-postaukseen. Instagram näyttää automaattisesti vain ensimmäiset kymmenen kommenttia. Lisää kommentteja saa näkyviin painamalla + -nappia postauksen alta, näin saa aina 10 kommenttia auki kerrallaan. Tämänkin kohdan voisi automatisoida, mutta tätä varten klikkailin kommentit auki käsin.

### 2. Tallenna aineisto lähdekoodista
Avaa Inspector painamalla F12. Klikkaa aunneesta paneelista vasemmasta yläkulmasta ikonia, jossa näkyy nuoli laatikon päällä ("Inspector"). Vie hiiri kommentin päälle. Nyt paneeliin ilmestyy maalattuna sen kohdan lähdekoodi jonka päälle veit hiiren. Etsi ja valitse lähdekoodista <div> jonka sisällä kaikki kommentit ovat. Klikkaa hiiren oikealla puolella ja valitse "copy inner HTML".

### 3. Liitä koodi Visual Studio Codeen ja tallenna tiedosto .html -päätteellä. 
Valitse koko tekstimassa (Ctrl+A / Cmd+A) ja avaa haku (Ctrl+P / Cmd+P), kirjoita avautuvaan hakukenttään ">Format Document" ja paina Enter.

### 4. Poimi ja tallenna kommentit
Kirjoitetaan bash shell script jonka avulla poimimme kaikesta tekstimassasta ainoastaan kommentit. 

Avaa terminaali ja mene kansioon johon tallensit tiedoston. Sitten kirjoita:

```
cat tallennetunTiedostonNimi.html | sed -n '/sqdOP yWXZ7d     _8A5w5   ZlAjV/,/class=""/p' | grep 'class=""' | sed 's/^ *//;s/ *$//' | sed 's/<span class="">//' | sed 's/<\/span>//' > uudenTiedostonNimi.html
```

* cat = lukee ja tulostaa (create output) tiedoston terminaaliin tai seuraavalle komennolle käsittelyyn 
* | = "putkitus" jonka avulla voidaan liittää useita komentoja yhteen
* ensimmäinen sed-komento: etsii ja tulostaa rivit joissa on '/sqdOP yWXZ7d     _8A5w5   ZlAjV' ja 'class=""' ja tulostaa näiden väliin jäävän osuuden
* sqdOP yWXZ7d     _8A5w5   ZlAjV/,/class=""/ = luokka joka esiintyy a -tagissa ennen kommenttia
* grep = etsii ja tulostaa rivit tekstistä joissa esiintyy 'class=""'
* sed 's/^ *//;s/ *$//' tämä tarvitaan jotta poistetaan tyhjät välilyönnit kommenttien edestä ja jälkeen
 * s = search
 * /^ * rivin alusta kaikki tyhjät välilyönnit
 * // korvaa edellä mainitut ei-millään
 * ; jatka sed-komentoa (tai aloita uusia sed komento)
 * / *$ rivin lopusta kaikki tyhjät välilyönnit
 * // korvaa edellä mainitut ei millään

* sed 's/<span class="">//' etsi kaikki kohdat joissa span tag aukeaa ja korvaa se ei-millään
* sed 's/<\/span>//' etsi kaikki kohdat joissa span tag suljetaan ja korvaa se ei-millään
* &gt; uudenTiedostonNimi.html tallentaa tuloksen tämän nimiseen tiedostoon (kirjoittaa yli, jos tämän niminen tiedosto on jo olemassa)

### 5. Tallenna haluamaasi muotoon
Kommentit löytyvät nyt jokainen omalta riviltään. Tekstimassan voi kopioida ja liittää haluamaansa ohjelmaan, esimerkiksi taulukkolaskentaohjelmaan, jossa itse tekstianalyysi voidaan tehdä. 

