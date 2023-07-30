## Encode User Permissions in Bytes [GOlang]
#### Skrypt zapisuje uprawnienia użytkowników z bazy danych, np. pracowników firmy, w formie ciągu zer i jedynek.
#### W skrypcie zastosowałem: bit shifting, iota w bloku const, operatory bitowe AND, OR.
#### Skrypt znajdzie zastosowaie w systemie zarządzania dostępem w organizacji. Pokazuje trzy mechanizmy backendu takiego systemu:
1. Generowanie zapisu bitowego dla każdego typu uprawnienia
2. Tworzenie roli jako zlepku uprawnień z wartościami binarnymi
3. Sprawdzenie czy dana rola* ma nadane określone uprawnienie

* Wielu użytkowników może mieć nadaną tą samą rolę, z tym samym szeregiem uprawnień. 

```golang
package main

import (
      "fmt"
)

//Uprawnienia zapisuję w formie bloku wartości stałych z przesunięciem bitu o iotę. Iota powiela wzór, czyli
//przy pierwszym uprawnieniu przesunięcie jest o 0. Przy drugim o 1 bit w lewo.
const {
      isAdmin = 1 << iota //Przesuwamy w lewo, czyli mnożymy 2 do potęgi x, gdzie x to liczba bitów.
      isHumanResources // 1(1) << 1 == 2(10)
      canSeeContracts // 2(10) << 2 == 8(1000) -> zapis bitowy. 2 * 2^2 = 8 zapis decymalny.

      canSeeCustomerService // 1000 << 3 == 1000000 -> zapis bitowy. 8 * 2^3 = 64 zapis decymalny
      canSeeSalesMarketing //itd.
      canSeeAccountingHR
      canSeeManagementTeam
      canSeeExecutiveBoard
}
//Łączymy wyniki poszczególnych uprawnień w jeden ciąg bitów.
func main() {
      var roles byte = isAdmin | canSeeContracts | canSeeSalesMarketing // == 18(10010)
      fmt.Printf("%b\n", roles)
      fmt.Printf("Is Admin? %v\n", isAdmin & roles == isAdmin) // 00001 & 10010 == 00000 == 0 (False)
      fmt.Printf("Is HR? %v\n", isHumanResources & roles == isHumanResources) // 00010 & 10010 == 00010 == 1 (True)

}
```
Output:
```golang
Is Admin? False
Is HR? True
```
Do rozwiązania operacji na bitach należy przyrównać do siebie poszczególne bity poprzez operator AND (&).
00010
10010
...1.
Widzimy, że tylko na 3. pozycji (licząc od zera) w obu liczbach mamy 1, więc tylko tam wynik to True.

To czyni całą operację różną od zera, a więc również True.

Jednocześnie, znaczy to, że uprawnienie "isHumanResources" znalazło się w grupie uprawnień "roles" tego użytkownika.
