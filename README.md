# Test

Úlohou je vytvoriť jednoduchý zoznam produktov s filtráciou. Splňovať by malo nasledujúce parametry:
- do pripravenej Vue apky (namiesot HelloWorld) vykresliť zoznam produktov
- produkty sa budú načítať z API (môže byť použité napr. https://fakestoreapi.com/products)
  - na načítanie produktov použiť Fetch API
  - použiť async/await syntax namiesto promise
- produkty budú vykreslené ako dlaždice (inšpirovať sa na aktin.cz). Dlaždica pre produkt bude ako samostatná komponenta.
- z načitaných produktov sa vytvorí filter na kategorie (ukážkový JSON má klíč "category")
  - hodnoty filtra budú vykreslené ako checkboxy (umiestnené ľubovoľne)
  - v zátvorke budú obsahovať počet produktov (napr. "jewelery (3)")
  - hodnoty budý zoradené podla počtu produktov (kategórie s najvačším počtom produktov budú prvé)
  - po zakliknutí checkboxu sa ihneď vyfiltrujú produkty (filtrovať sa budú iba načítané produkty tzn. neni potrebné riešit filtrovanie cez API)
  - pokial bude aktívna hodnota tak sa zobrazí tlačitko na resetovanie filtrov
  - aktívne hodnoty filtra sa budú cez History API prepisovat do URL adresy a pri opetovnom načítaní (alebo refreshi stránky) sa z URL načítajú a aktivujú
- není povolené použiť žiadne externé knižnice (okrem už nainštalovaného Vue samozrejme)

## Spustenie projektu

docker + nginx-proxy

```sh
ln -s docker-compose.test.yml docker-compose.override.yml
docker-compose build
docker-compose up
```
