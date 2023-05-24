## Extract Unique Options from Partly Overlapping 
#### Skrypt wyodrębnia unikalne opcje, których nazwa częściowo się pokrywa: parking / parking strzeżony / parking, dodatkowo płatny.
#### W tym skrypcie zastosowałem: pętlę for, podwójnie zagnieżdżoną instrukcję warunkową, wyszukiwanie słów kluczowych w łańcuchu tekstowym i liczenie znaków przez wskaźnik. Skrypt ma zastosowanie wewnętrzne, jest dostosowany do danych, na których pracowałem.
#### Rozwiązana przeszkoda polegała na tym, że treść opcji była zapisana w sposób niejednoznaczny, przecinek oddziela poszczególne opcje, ale występuje także w samej treści opcji, np. "parking strzeżony, dodatkowo płatny". Z tego powodu zwykłe podzielenie tekstu względem przecinka błędnie potraktowałoby "dodatkowo płatny" jako samodzielną opcję, zamiast potraktować to jako wydłużenie nazwy opcji, które czyni ją samą unikalną względem tych niezawierających dopisku. 

#### Spis treści:
1. Kod skryptu
2. Wyjaśnienie działania skryptu na przykładzie
3. Input : zawartość zmiennej PRZED zastosowaniem skryptu 
4. Output: zawartość zmiennej PO zastosowaniu skryptu

### 1. KOD SKRYPTU
Wyciągam komórki kolumny "parking" do zmiennej; każda komórka będzie elementem zbioru.
```python opcje_parkingu = set()
_ = train.parking.map(lambda x: opcje_parkingu.add(x) if pd.notna(x) else None)
```
Zamieniam zbiór na ciąg tekstowy i na listę pozbywając się przecinków, które znacznie komplikowałyby robotę.
```python opcje_parkingu = str(opcje_parkingu)
opcje_parkingu2 = list()
for cell in opcje_parkingu.split(','):
    cell2 = re.sub('[^\w\s]','',cell)
    cell2 = cell2[1:] if cell2[0]==' ' else cell2
    opcje_parkingu2.append(cell2)
```
Skrypt parsuje ciąg tekstowy w poszukiwaniu słów kluczowych, które oznaczają początek i koniec nowej opcji parkingu (nowa opcja z tej kolumnie zawsze zaczyna się jednym z słów kluczowych, więc kiedy przeczyta kolejne słowo-klucz cofa się na jego początek i odcina poprzedni ciąg wysyłając go do zbioru unikalnych opcji parkingu.
```python uniq_opcje_parkingu = set()
kluczowe = ['brak', 'garaż', 'parking', 'miejsce']

for idx, cell in enumerate(opcje_parkingu2):
    if any(cell.startswith(klu) for klu in kluczowe):
        if idx < len(opcje_parkingu2)-1 and not any(opcje_parkingu2[idx+1].startswith(klu) for klu in kluczowe):
            if idx < len(opcje_parkingu2)-2 and not any(opcje_parkingu2[idx+2].startswith(klu) for klu in kluczowe):
                unikalna = opcje_parkingu2[idx] + ' ' + opcje_parkingu2[idx+1] + ' ' + opcje_parkingu2[idx+2]
            else:
                unikalna = opcje_parkingu2[idx] + ' ' + opcje_parkingu2[idx+1]
        else:
            unikalna = opcje_parkingu2[idx]
    else:
        pass
    uniq_opcjeparkingu.add(unikalna)
    
uniq_opcjeparkingu
```
### 2. WYJAŚNIENIE

Dane pochodzą z tabeli typu Dataframe dotyczącego nieruchomości mieszkalnych, z kolumny "opcje parkingu". 
Skrypt wyodrębnia unikalne opcje, których treść się częsciowo pokrywa - na tym polega trudność zadania.

Zilustruję to przykładem: dwie komórki zawierają tekst:

* jedna: "garaż, parking strzeżony, płatny dodatkowo, parking podziemny"

* druga: "parking strzeżony, parking garaż"


Nasz skrypt wyodrębnia następujące unikalne opcje parkingu:

* garaż

* parking strzeżony płatny dodatkowo*

* parking podziemny*

* parking strzeżony*

* parking*

*Zwróć uwagę, że opcje oznaczone gwiazdką pokrywają się częścią treści, a mimo to są odrębnymi opcjami. 

### 3. INPUT
```python
opcje_parkingu
{'brak',
 'garaż',
 'garaż, brak',
 'garaż, miejsce parkingowe',
 'garaż, miejsce parkingowe, parking podziemny',
 'garaż, miejsce parkingowe, parking podziemny, płatny dodatkowo',
 'garaż, miejsce parkingowe, parking podziemny, płatny dodatkowo, obowiązkowy',
 'garaż, miejsce parkingowe, parking strzeżony',
 'garaż, miejsce parkingowe, parking strzeżony, parking podziemny',
 'garaż, miejsce parkingowe, płatny dodatkowo',
 'garaż, parking podziemny',
 'garaż, parking podziemny, płatny dodatkowo, obowiązkowy',
 'garaż, parking strzeżony',
 'miejsce parkingowe',
 'miejsce parkingowe, brak',
 'miejsce parkingowe, możliwość wynajęcia',
 'miejsce parkingowe, parking podziemny',
 'miejsce parkingowe, parking podziemny, brak',
 'miejsce parkingowe, parking podziemny, płatny dodatkowo',
 'miejsce parkingowe, parking strzeżony',
 'miejsce parkingowe, parking strzeżony, parking podziemny',
 'miejsce parkingowe, parking strzeżony, płatny dodatkowo',
 'możliwość wynajęcia',
 'parking podziemny',
 'parking podziemny, brak',
 'parking podziemny, płatny dodatkowo',
 'parking podziemny, płatny dodatkowo, możliwość wynajęcia',
 'parking podziemny, płatny dodatkowo, obowiązkowy',
 'parking strzeżony',
 'parking strzeżony, brak',
 'parking strzeżony, parking podziemny',
 'płatny dodatkowo'}
```
### 4. OUTPUT
```python
uniq_opcjeparkingu
{'brak',
 'brak płatny dodatkowo',
 'garaż',
 'garaż możliwość wynajęcia',
 'miejsce parkingowe',
 'miejsce parkingowe możliwość wynajęcia',
 'miejsce parkingowe płatny dodatkowo',
 'parking podziemny',
 'parking podziemny płatny dodatkowo',
 'parking podziemny płatny dodatkowo możliwość wynajęcia',
 'parking podziemny płatny dodatkowo obowiązkowy',
 'parking podziemny płatny dodatkowomożliwość wynajęcia',
 'parking podziemny płatny dodatkowoobowiązkowy',
 'parking strzeżony',
 'parking strzeżony płatny dodatkowo'}
```
