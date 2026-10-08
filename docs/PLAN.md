# Delovni pult: lokalno delovno orodje

Plan in zgodovina zahtev. Dokument je referenca za vse nadaljnje odločitve. Ko se zahteva spremeni, jo dopišemo v razdelek 9 (dnevnik sprememb), prejšnje različice ne brišemo.

Stanje: **plan v2 potrjen za prvi korak. V izdelavi: samo kanban to-do stran (`orodje/index.html`).** Ostale faze sledijo po preizkusu.

---

## 1. Izvirna zahteva (dobesedno, 8. 10. 2026)

> Želim pripraviti zelo uporabno orodje, ki ga bi lahko uporabljal v službi, ki je zaprto omrežje ne smem poganjati posebnih skript za omrežju in podobno. Orodje mora biti enostavno. Lahko določen del podajam v mojem cPanelu spletni strani če bo potrebno.
>
> Želel si bi imeti lokalno transkripcijo sestankov, da bi lahko vedno delal povzetke. To bi moralo vse teči lokalno če je mogoče. Mali lokalni model ali podobno.
>
> Možnost, da bi sledil emailom, kje je še kaj ostalo, uporabljam 3CX ali lahko tukaj si kaj pomagam brez da bi preveč kompleksno naredili.
>
> Želim, da bi lokalno integriral moja orodja, da bi samo sledil taske, to do v uporabnem vmesniku, tako bi imel pregled nad mojimi priložnostmi, projekti, lokacije datotek na omrežnem disku in podobno. Zelo enostavni procesi znotraj mojega PCja.
>
> Zunanji AI bi naknadno dodali mogoče za omejene informacije sam moramo biti complient. GDPR in vsa evropska direktiva.

Druga navodila (8. 10. 2026): najprej podroben plan, vse informacije o zahtevah zabeležene za zgodovino odločitev.

Povratna informacija na plan v1 (8. 10. 2026, dobesedno):

> Ok 3CX in OUTLOOK staja težava ker ne smemo integrirati. In ne morem uvažati saj je zamudno.
>
> Ostalo bi šlo. Spet so omejitve z glasovnim snemanjem in obdelavo zakonsko

Navodilo za prvi korak (8. 10. 2026, dobesedno):

> Naredi samo web page z too doo.
>
> Imamo WEBGPU in lahko dostopam do Cpanel domene.
>
> Potem gremo naprej.
>
> Običajo je to kanban, nekaj so potem znotraj tega posamezne priložnosti sledenje

## 2. Zahteve, razčlenjene

| ID | Zahteva | Prioriteta |
|----|---------|-----------|
| Z1 | Deluje v zaprtem službenem omrežju, brez nameščanja programov in brez poganjanja skript (PowerShell, Python, .exe ipd.) | obvezno |
| Z2 | Enostavno za uporabo in vzdrževanje | obvezno |
| Z3 | Del se lahko gosti na mojem cPanel strežniku | dovoljeno |
| Z4 | ~~Lokalna transkripcija sestankov (slovenščina)~~ **v2:** snemanje glasu in obdelava posnetkov sta zakonsko omejena; namesto tega hitri zapiski med sestankom (glej O3 v2) | spremenjeno |
| Z5 | Povzetki sestankov, po možnosti z majhnim lokalnim modelom; **v2:** povzetek iz mojih zapiskov, ne iz posnetka | obvezno (osnovno), AI povzetek zaželen |
| Z6 | Sledenje emailom: kje še čakam odgovor, kaj je ostalo odprto; **v2:** brez integracije in brez uvoza, samo zelo hiter ročni vnos | obvezno |
| Z7 | ~~3CX: sledenje klicem~~ **v2:** brez integracije in uvoza; klici se beležijo enako kot emaili (hiter ročni vnos) | spremenjeno |
| Z14 | **v2:** Vnos mora biti hitrejši od uvoza: ena vrstica, brez obrazcev | obvezno |
| Z8 | Opravila / to-do v preglednem vmesniku | obvezno |
| Z9 | Pregled priložnosti (prodajni pipeline) | obvezno |
| Z10 | Pregled projektov | obvezno |
| Z11 | Lokacije datotek na omrežnem disku, povezane s projekti/strankami | obvezno |
| Z12 | Kasneje: zunanji AI za omejene, anonimizirane podatke | kasneje |
| Z13 | Skladnost z GDPR in evropsko zakonodajo (tudi AI Act) | obvezno |

## 3. Omejitve okolja in kaj iz njih sledi

**Zaprto omrežje, brez skript in namestitev.** Edino, kar je na službenem PC skoraj zagotovo dovoljeno, je spletni brskalnik (Edge ali Chrome). Zato je orodje **spletna stran, ki teče v brskalniku**. Brskalnik danes zmore:

- shranjevanje podatkov lokalno (IndexedDB),
- poganjanje AI modelov lokalno (WebAssembly in WebGPU), brez strežnika.

Brskalnik nalaga samo statične datoteke (HTML, JS, model). **Nobeni podatki ali besedila ne zapustijo PC-ja.**

**Kje teče kaj:**

| Del | Kje |
|-----|-----|
| Uporabniški vmesnik, opravila, priložnosti, projekti | brskalnik na službenem PC |
| Podatki | brskalnik (IndexedDB) + varnostna kopija JSON na omrežnem disku |
| ~~Transkripcija (Whisper)~~ | v2: opuščeno |
| AI povzetek (majhen LLM) | brskalnik na službenem PC (WebGPU), opcijsko |
| cPanel | samo gostovanje statičnih datotek: aplikacija, knjižnica, datoteke modelov. Strežnik nikoli ne vidi vsebine. |

## 4. Ključne odločitve (z utemeljitvijo)

**O1. Ena HTML aplikacija, brez namestitve.** Vanilla JavaScript, brez build koraka, brez odvisnosti razen knjižnice za AI. Vzdrževanje je odpiranje ene datoteke.

**O2. Dva načina zagona.**
- **A (priporočeno): gostovanje na cPanel** pod HTTPS (npr. `https://orodje.mojadomena.si`), zaščiteno z geslom (`.htaccess`). Razlogi: brskalnik lahko modele (250 MB do 1 GB) shrani v predpomnilnik, kar iz lokalne datoteke ne gre; večnitni WASM zahteva posebne glave (COOP/COEP), ki jih nastavimo v `.htaccess`.
- **B: lokalna datoteka** (`file://`, npr. z omrežnega diska). Vse razen opcijskega AI povzetka (F4) deluje v celoti; model za AI bi se nalagal vsakič znova. **v2:** brez transkripcije je način B povsem enakovreden, cPanel je potreben samo za AI povzetek. Rezervna možnost, če službeno omrežje blokira mojo domeno.

Odprto vprašanje V1: ali službeni požarni zid dovoli dostop do moje domene?

**O3 v2 (velja). Brez snemanja glasu: hitri zapiski med sestankom.**
Razlog: zakonske omejitve snemanja in obdelave glasu (povratna informacija 8. 10. 2026). Orodje ne dostopa do mikrofona in ne obdeluje zvočnih posnetkov.
- Način "Sestanek": velik vnos, kjer med sestankom tipkam kratke vrstice. Vsaka vrstica dobi časovno oznako.
- Bližnjice na začetku vrstice: `!` naloga (moja), `@Ime` naloga za drugo osebo, `?` odprto vprašanje, `=` dogovor/odločitev, `$` cena/vrednost, `>` naslednji korak z rokom (npr. `> ponudba pet`).
- Ob koncu orodje samo sestavi zapisnik (udeleženci, dogovori, naloge, odprta vprašanja, naslednji koraki); moje naloge gredo z enim klikom med opravila, naloge drugih v "Čakam na".
- Predloga zapisnika se kopira v email ali Word.
- Opcija za kasneje, samo če jo podjetje izrecno dovoli: prilepiti prepis, ki ga ustvari že odobreno orodje (npr. Teams). Ne gradimo vnaprej.

**O3 v1 (opuščeno 8. 10. 2026, zakonske omejitve). Transkripcija: Whisper v brskalniku** prek knjižnice Transformers.js (Hugging Face, Apache 2.0, različica 4.2.0).
- Privzeti model `whisper-small` (multilingual, kvantiziran, okoli 250 MB): razumna slovenščina, deluje tudi brez grafične kartice.
- Opcija `whisper-large-v3-turbo` (okoli 800 MB) za boljšo kakovost, če ima PC WebGPU.
- Izbira modela v nastavitvah.
- Zvok se razreže na okoli 30 s kose na tišinah, sproti se kaže napredek in delni rezultat, s časovnimi oznakami `[mm:ss]`, možna prekinitev.
- Viri zvoka: (1) snemanje mikrofona, (2) mikrofon + zvok sestanka (deljenje zavihka/zaslona z zvokom, za Teams/3CX), (3) nalaganje datoteke (mp3, m4a, wav, webm).
- Posnetek si lahko shranim lokalno; aplikacija ga privzeto ne hrani.
- Pričakovana hitrost (ocena, preveriti na službenem PC): z WebGPU nekaj minut za uro sestanka; samo CPU lahko 20 do 40 minut za uro. Diarizacija (kdo govori) v prvi različici ni vključena.

**O4 v2 (velja):** enaka dva nivoja, a vhod so moji zapiski namesto transkripta. Hitri povzetek iz bližnjic (`!`, `?`, `=`, `>`) je zanesljiv, ker strukturo določim sam med tipkanjem. Lokalni AI povzetek ostane opcija za lepšo besedilno obliko (F4, eksperimentalno).

**O4 v1. Povzetki v dveh nivojih.**
- **Hitri povzetek (vedno na voljo, brez AI):** pravila za slovenščino iz transkripta potegnejo dogovore, naloge, roke/datume, odprta vprašanja in ključne teme v predlogo zapisnika. Naloge se z enim klikom pretvorijo v opravila.
- **AI povzetek (eksperimentalno, lokalno):** majhen jezikovni model (privzeto Qwen2.5 1.5B Instruct, okoli 1 GB, zahteva WebGPU). Dolg transkript se povzema po delih, nato skupaj. Opozorilo: majhni modeli so v slovenščini omejeni. Model je zamenljiv v nastavitvah.

**O5 v2 (velja). Sledenje "Čakam na" z vnosom v eni vrstici.** Brez integracije, brez uvoza, brez prilepljanja emailov.
- Globalna bližnjica (npr. `Ctrl+K` ali tipka `c`) odpre eno vrstico, kjer napišem npr. `Novak ponudba 3d` ali `klic Kovač servis jutri`.
- Orodje prepozna vrsto (email privzeto, `klic` če je napisano), osebo/stranko (iz seznama strank, sicer prva beseda), rok (`3d`, `jutri`, `pet`, `15.10.`) in povezavo na priložnost/projekt, če stranka obstaja.
- V pregledu "Danes" so zapadli na vrhu; en klik: "urejeno", "+2 dni" ali "pretvori v opravilo".
- Ni gumbov `mailto:` in ne `tel:`, da ni nobene povezave z Outlookom ali 3CX.

**O5 v1 (opuščeno 8. 10. 2026, ni dovoljeno integrirati, uvoz je zamuden). Sledenje emailom brez integracije v Outlook/Exchange.** Neposreden dostop do pošte bi zahteval skripte ali API dovoljenja (krši Z1). Zato:
- seznam "Čakam odgovor": zadeva, oseba, datum poslano, opomni čez N dni, stanje;
- hiter vnos: prilepi besedilo emaila ali povleci `.eml` datoteko, aplikacija prebere zadevo, pošiljatelja, datum;
- gumb "Pošlji opomnik" odpre nov email (`mailto:`) z "Re: zadeva";
- v pregledu "Danes" se pokažejo zapadli.

Dopolnilo v README: nasvet za Outlookove zastavice (Follow up) in iskalno mapo, ki delujeta brez dodatkov.

**O6 v2 (velja): 3CX se v orodju ne pojavlja.** Klici se vodijo v istem seznamu "Čakam na" (O5 v2).

**O6 v1 (opuščeno 8. 10. 2026). 3CX brez integracije.**
- Telefonske številke v aplikaciji so povezave `tel:`; klik zažene klic v nameščeni 3CX aplikaciji.
- Uvoz CSV zgodovine klicev (izvoz iz 3CX): samodejno prepoznavanje stolpcev, filter zgrešenih/neodgovorjenih, ustvarjanje vnosov "Povratni klic".
- Hitri ročni zapis klica (kdo, kaj, naslednji korak).
- Gumb za odprtje 3CX spletnega odjemalca (URL v nastavitvah).

Odprto vprašanje V2: ali lahko v 3CX izvozim zgodovino klicev kot CSV (odvisno od pravic)?

**O7. Podatkovni model.**

| Zbirka | Polja (bistvena) |
|--------|------------------|
| Opravila | naslov, stanje (za narediti / v delu / čakam / narejeno), rok, prioriteta, projekt, priložnost, opombe, vir |
| Priložnosti | stranka, naziv, vrednost EUR, faza (Lead, Kvalifikacija, Ponudba, Pogajanja, Dobljeno, Izgubljeno), verjetnost, naslednji korak + datum, kontakt, opombe |
| Projekti | naziv, stranka, stanje, mapa na omrežnem disku, opombe |
| Sledenje | vrsta (email/klic/drugo), zadeva, oseba, kontakt, poslano, opomni dne, stanje, povezava na projekt/priložnost |
| Sestanki | naziv, datum, udeleženci, transkript, povzetek, opombe, povezava |
| Mape in datoteke | oznaka, pot (UNC ali pogon), oznake, povezava na projekt/stranko |

**O8. Vmesnik (slovenščina).**
- **Danes:** zapadla in današnja opravila, sledenja za danes, priložnosti z rokom naslednjega koraka, zadnji sestanki, vrednost pipeline-a (tudi utežena).
- **Opravila:** kanban (povleci in spusti) + seznam.
- **Priložnosti:** kanban po fazah + tabela.
- **Projekti:** seznam, ob kliku vse povezano (opravila, sestanki, mape, sledenja).
- **Sledenje:** emaili in klici, uvoz CSV in `.eml`.
- **Sestanki:** snemanje, transkripcija, povzetek, pretvorba nalog v opravila.
- **Mape:** gumb "Kopiraj pot" (prilepim v Raziskovalec; brskalniki iz varnostnih razlogov ne odpirajo map neposredno).
- **Globalno iskanje** čez vse.
- **Hiter vnos:** npr. `Pokliči Novak jutri !1 #ProjektX` (datumi: danes, jutri, dnevi v tednu, dd.mm.).
- Svetla in temna tema.

**O9. Shranjevanje in varnostne kopije.**
- Primarno: IndexedDB v brskalniku.
- Samodejna varnostna kopija v izbrano JSON datoteko (npr. na osebni omrežni mapi) prek File System Access API (Edge/Chrome).
- Ročni izvoz/uvoz JSON, izvoz CSV za Excel.
- Opozorilo v README: brisanje podatkov brskalnika izbriše tudi lokalne podatke, zato je varnostna kopija pomembna.

**O10. Zunanji AI (kasneje, Z12) pripravimo, ne vklopimo.**
V prvi različici aplikacija **ne pošilja ničesar nikamor**. Pripravimo:
- **Anonimizator:** zamenja emaile, telefonske številke, IBAN, davčne/matične številke, imena strank in oseb iz mojih seznamov s psevdonimi (`[OSEBA_1]`, `[PODJETJE_2]`); pokaže predogled in tabelo zamenjav.
- Gumb "Kopiraj za zunanji AI" s predlogo navodila; odgovor prilepim nazaj in aplikacija vrne prava imena.
- Stikalo "Zunanji AI dovoljen" je privzeto izklopljeno.

Neposredno povezavo na zunanji API dodamo šele po odobritvi v podjetju (razdelek 5).

## 5. GDPR in EU skladnost

| Tema | Kako je naslovljeno |
|------|---------------------|
| Minimizacija in lokalna obdelava (GDPR čl. 5, 25) | vsa obdelava na PC; strežnik dobi samo statične datoteke |
| Snemanje sestankov | **v2: ni snemanja, ne obdelave glasu.** (v1: pred snemanjem opomnik: obvesti udeležence in pridobi soglasje; zapisan čas obvestila. Pravna podlaga je odvisna od internega pravilnika, preveriti s pooblaščencem za varstvo podatkov (DPO). Sodi tudi v ZVOP-2.) |
| Hramba | posnetek se privzeto ne hrani; transkript lahko izbrišem; opomnik za čiščenje starih sestankov (nastavljiv rok, npr. 90 dni) |
| Pravica do izbrisa / vpogleda | iskanje po osebi, izvoz, izbris |
| Varnost (čl. 32) | podatki na službenem PC v profilu brskalnika (BitLocker, prijava v domeno); cPanel pod HTTPS in z geslom; brez piškotkov, sledenja ali zunanje analitike |
| Zunanji AI (kasneje) | samo anonimizirani podatki; ponudnik z DPA pogodbo in obdelavo v EU; vpis v evidenco dejavnosti obdelave; po potrebi DPIA |
| EU AI Act | transkripcija in povzemanje za lastno rabo nista visoko tvegani sistemi; obveznost AI pismenosti (čl. 4) in preglednost do udeležencev, da se uporablja AI zapis |
| Licence | Transformers.js Apache 2.0; Whisper MIT; Qwen2.5 Apache 2.0 (preveriti ob izbiri modela) |

Pomembno: preden orodje uporabim za sestanke s strankami, naj pregled opravi DPO ali IT varnost v podjetju. Plan ni pravni nasvet.

**O11 (8. 10. 2026). Prvi korak je samo kanban to-do stran.**
- Ena tabla (kanban) s stolpci: Za narediti, V delu, Čakam, Narejeno.
- Vsaka kartica ima vrsto: **Opravilo**, **Priložnost** ali **Sledenje** (čakam na odgovor/klic). Priložnosti in sledenja torej živijo kot kartice na isti tabli, ne v ločenih modulih. To nadomesti ločene poglede iz O8 za prvi korak.
- Znotraj kartice: stranka, rok, prioriteta, vrednost (priložnost), pot do mape, opombe, podnaloge, **dnevnik sledenja** (zapisi s časom: kaj se je zgodilo, naslednji korak).
- Hiter vnos v eni vrstici (Z14), npr. `Novak ponudba jutri !1 #p 5000€ @Novak d.o.o.`.
- Shranjevanje v brskalniku + samodejna varnostna kopija v datoteko + izvoz/uvoz JSON.
- Gostovanje na cPanel ali odpiranje kot lokalna datoteka.

## 6. Struktura repozitorija

```
docs/PLAN.md              ta dokument
orodje/index.html         celotna aplikacija (HTML + CSS + JS)
orodje/.htaccess          za cPanel: geslo, HTTPS, COOP/COEP, predpomnjenje
orodje/lib/               (opcijsko) lastna kopija Transformers.js in ONNX Runtime
orodje/models/            (opcijsko) lastna kopija modelov
orodje/prenesi-modele.ps1 pomožna skripta, ki jo poženem DOMA (ne v službi): prenese knjižnico in modele za nalaganje na cPanel
README.md                 navodila za namestitev in uporabo (slovensko)
```

Skripta za prenos modelov teče na domačem računalniku, v službi se nič ne poganja.

## 7. Faze izvedbe

| Faza | Vsebina | Rezultat |
|------|---------|----------|
| F1 | Ogrodje, shranjevanje (IndexedDB), varnostne kopije, opravila, priložnosti, projekti, mape, iskanje, pregled Danes, hiter vnos | uporabno orodje za vsak dan |
| F2 | "Čakam na": vnos v eni vrstici za emaile in klice, opomniki v pregledu Danes | Z6, Z7, Z14 |
| F3 | Sestanki: hitri zapiski z bližnjicami, samodejni zapisnik, naloge v opravila | Z4 v2, Z5 osnovno |
| F4 | AI povzetek zapiskov z lokalnim LLM (eksperimentalno, opcijsko) | Z5 polno |
| F5 | Anonimizator in priprava za zunanji AI, GDPR funkcije (čiščenje starih zapisov, izbris po osebi) | Z12, Z13 |
| F6 | cPanel paket (`.htaccess`), README | Z3 |

Faze v1 (zgodovina): F2 je vključeval `.eml` in 3CX CSV uvoz, F3 snemanje in Whisper transkripcijo. Opuščeno 8. 10. 2026.

Po F1 in F3 je smiselno preizkusiti na službenem PC in plan po potrebi popraviti.

## 8. Tveganja in odprta vprašanja

| ID | Vprašanje / tveganje | Vpliv | Ukrep |
|----|----------------------|-------|-------|
| V1 | ~~Dostop do cPanel domene?~~ | zaprto 8. 10. 2026: **da**, način A (cPanel) je izvedljiv | |
| V2 | ~~Ali 3CX dovoli izvoz CSV?~~ | zaprto 8. 10. 2026: 3CX se ne integrira | |
| V3 | ~~WebGPU?~~ | zaprto 8. 10. 2026: **da**, lokalni AI povzetek (F4) je izvedljiv | |
| V4 | ~~Mikrofon/deljenje zaslona?~~ | zaprto 8. 10. 2026: brez snemanja | |
| V5 | ~~Kakovost transkripcije~~ | zaprto 8. 10. 2026: brez transkripcije | |
| V6 | Kakovost slovenskih povzetkov z majhnim LLM | uporabnost | hitri povzetek brez AI kot osnova; kasneje zunanji AI z anonimizacijo |
| V7 | Ali je dovoljeno v zapiske vpisovati osebne podatke udeležencev (imena, kontakte)? Verjetno da, kot običajni poslovni zapisnik | pravna podlaga | potrditi z DPO |
| V8 | Ali podjetje uradno dovoljuje transkripcijo v Teams? | samo za morebitno kasnejšo opcijo (O3 v2) | ni nujno |
| V9 | Ali uporabljam Edge ali Chrome? | File System Access API deluje v obeh, v Firefoxu ne | priporočilo Edge/Chrome |

## 9. Dnevnik sprememb zahtev in odločitev

| Datum | Sprememba |
|-------|-----------|
| 2026-10-08 | Prva zahteva (razdelek 1). Odločitev za brskalniško aplikacijo, lokalni Whisper, cPanel samo za statične datoteke. |
| 2026-10-08 | Zahteva: najprej podroben plan, zgodovina zahtev v dokumentu. Implementacija ustavljena do potrditve plana. |
| 2026-10-08 | Povratna informacija na v1: integracija Outlook in 3CX ni dovoljena, uvoz je prezamuden, snemanje in obdelava glasu sta zakonsko omejena. Odločitve v2: brez snemanja in transkripcije (O3 v2: hitri zapiski z bližnjicami), sledenje emailom in klicem z vnosom v eni vrstici (O5 v2), 3CX izpuščen (O6 v2), nova zahteva Z14 (hitrost vnosa). Ostalo (O1, O2, O7 do O10) potrjeno. |
| 2026-10-08 | Potrjeno: WebGPU je na voljo, cPanel domena dostopna (V1, V3 zaprta). Prvi korak omejen na kanban to-do stran, kjer so priložnosti in sledenja kartice znotraj iste table (O11). |
