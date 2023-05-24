# Most Frequent Words in an Article 
#### Funkcja w kolejności malejącej drukuje najczęściej (tj. co najmniej 5 razy) użyte słowa wraz z licznikiem ich wystąpień. Słowa poniżej 5 znaków zapisane wyłącznie małymi literami traktuje jako prawdopodbny zaimek lub przyimek. Jest niewiele krótkich słów, często są to skrótowce lub nazwy własne, w których występuje wielka litera.
#### Skrypt może mieć zastosowanie w analizie dyskursu. Szczególnie przy dużych zbiorach tekstu. Analiza częstości słów pokazuje obiekty zainteresowania autora oraz charakteryzuje styl komunikatu. 
#### Najważniejsze metody: dict(), filter(), sorted(). Operacje na pliku, słowniku i definiowanie funkcji pomocniczych.
```python
with open('tekst do analizy artykuły.txt') as file:
    text = file.readlines()

words = {}
for line in text:
    for word in line.split(' '):
        if word not in words:
            words[word] = 1
        else:
            words[word] += 1

zaimki = set()
for key in words.keys():
    if len(key) < 5 and key.lower() == key:
        zaimki.add(key)
            
def filt(pair):
    word, count = pair
    if count >= 5 and word not in zaimki:
        return True
    else:
        return False

most_common_words = dict(filter(filt, words.items()))


def drukuj_slownik(a):
    b = sorted(a.items(), key = lambda x: x[1], reverse = True)
    for w in b:
        print(w)

drukuj_slownik(most_common_words)
```
