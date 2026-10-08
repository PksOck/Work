# Delovni pult

Kanban to-do za vsakdanje delo: opravila, priložnosti in sledenja (čakam na odgovor) na eni tabli. Ena HTML stran, brez namestitve, brez skript. Podatki ostanejo v brskalniku na tvojem računalniku.

Plan in zgodovina zahtev: [docs/PLAN.md](docs/PLAN.md).

## Zagon

**Na cPanel (priporočeno)**
1. V cPanel → File Manager ustvari mapo, npr. `public_html/pult/` (ali poddomeno).
2. Naloži `orodje/index.html` in `orodje/.htaccess`. Če `.htaccess` ni viden, v File Manager → Settings vklopi "Show Hidden Files".
3. cPanel → Directory Privacy → izberi mapo `pult` → vklopi geslo in dodaj uporabnika.
4. Odpri `https://tvojadomena.si/pult/` v Edge ali Chrome in si stran zaznamuj.

**Kot lokalna datoteka:** dvoklik na `index.html` (npr. z omrežnega diska). Deluje enako. Pozor: podatki so vezani na naslov strani, zato ne menjuj med cPanel in lokalno datoteko (ali prenesi podatke z Izvozi/Uvozi).

## Prvi koraki
1. **Meni → Nastavi datoteko za samodejno varnostno kopijo** in izberi datoteko na svojem omrežnem disku. Kopija se nato zapiše ob vsaki spremembi. Po ponovnem zagonu brskalnika klikni "potrdi" zgoraj desno.
2. **Meni → Pomoč** za sintakso hitrega vnosa.

## Hiter vnos (primeri)
| Vneseš | Dobiš |
|---|---|
| `Ponudba za ERP @Novak 15k€ pet !1` | priložnost, stranka Novak, 15.000 €, rok petek, visoka prioriteta |
| `Odgovor na pogodbo @Kovač #s 3d` | sledenje v stolpcu Čakam, rok čez 3 dni |
| `Pripravi demo #v 20.10.` | opravilo v stolpcu V delu, rok 20. 10. |

Tipke: `N` hiter vnos, `/` iskanje, `Esc` zapri.

## Podatki in GDPR
- Vse je shranjeno lokalno v brskalniku (localStorage) in v tvoji varnostni kopiji. Strežnik dobi samo statično stran.
- `.htaccess` z varnostno politiko (CSP) brskalniku prepove pošiljanje podatkov na katerikoli strežnik.
- Brisanje podatkov brskalnika izbriše tudi kartice, zato nastavi varnostno kopijo.
- Izbris: kartico izbrišeš v podrobnostih, za iskanje po osebi uporabi iskalno polje.
