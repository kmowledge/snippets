# Data filtering: type(not dict), skip duplicates
#### Proste skrypty, dzięki którym poznajemy bliżej dane w DataFrame, na którym pracujemy. Operacja odfiltrowania typu danych innych niż dict i usunięcia duplikatów, na parę sposobów.
#### Case użycia: spodziewanym typem wpisów w kolumnie jest słownik. Okazuje się, że są wyjątki od tego założenia. Odfiltrujemy je, a następnie wyświetlimy ich unikalne treści, tak by móc sprawnie je przeglądać (bez gąszczu komórek o identyntynczej treści).
 
#### Spis skryptów:
1. Odfiltrowanie i zliczenie komórek kolumny o innym typie, niż dict.
2. Drukowanie wyfiltrowanych komórek bez duplikatów (Sprawdzamy zawartość wyfiltrowanej grupy komórek, niesłowników). 

### 1. Odfiltrowanie i zliczenie komórek not dict.
```python
# 1a. pierwszy sposób: list comprehension + isinstance()
sum(not isinstance(x, dict) for x in train['building'])

# 1b. drugi sposób: filtr + map() + isinstance()
filtr3 = train['building'].map(lambda x: not isinstance(x, dict)) #.apply/.map

a = int()
for x in train['building'][filtr3]:
    a+=1
a

# 1c. trzeci sposób: list comprehension + type()
b = int()
b = sum(1 for x in train['building'] if type(x) != dict)
b
```
### 2. Drukowanie wyfiltrowanych komórek bez duplikatów
```python
#2a. pierwszy sposób: jeśli x nie jest taki jak ostatni, to wydrukuj go i przypisz do zmiennej odp. za ostatni.

count = int()
prev_x = None
for x in train['column_A'][filtr3]:
    if x != prev_x:
        print(x, type(x))
        prev_x = x
    count += 1
print(count)

#2b. drugi sposób: "jeśli treść jest już w liście, pomiń, jeśli nie, wydrukuj"

count = int()
lista = list()
for x in train['column_A'][filtr3]:
    _ = x, type(x)
    if _ in lista:
        pass
    else:
        print(_)
    lista.append(_)
    count += 1
print(count)

#(2c. trzeci sposób: dodawanie każdego iterowanego elementu x do zbioru
#     i wydrukowanie zbioru po zakończeniu pętli - typ danych set() przechowuje tylko unikaty)
```
