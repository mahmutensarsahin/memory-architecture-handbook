# C — Diziler, Pointerlar ve Decay (Akademik Notlar)

---

## 1) Array Etiketi ve Adresleme

* **Kavram:** Bir `array` tanımlandığında C’de derleyici, array’e bir etiket (label) atar; bu label **taban adresini** (`&arr[0]`) gösterir.
* **Özellik:** Bu taban adresi ayrı bir pointer değildir; array’in kendisine karşılık gelir.
* **Mini Senaryo:**

```c
int arr[3] = {1,2,3};
int *p = arr;  // arr → &arr[0], pointer decay ile tip T* oluyor
```

* **Pseudo-Assembly:**

  ```
  resolve arr -> taban adres
  load [addr + i*sizeof(int)] -> r
  ```
* **ASCII Şeması:**

```
[arr] [e0][e1][e2]
      ^ base
```

* **Derleyici Notu:** Global arraylerde linker kesin adrese bağlar, lokal arraylerde SP tabanlı offset kullanılır.

---

## 2) Array-to-Pointer Decay

* **Kavram:** Çoğu bağlamda array ismi otomatik olarak `T*` pointer’a dönüşür (`decay`).
* **Sonuç:** Boyut bilgisi kaybolur; pointer aritmetiği kullanılır.
* **Mini Senaryo:**

```c
int arr[3] = {1,2,3};
int *p = arr;
int x = arr[1];  // x = *(arr+1)
```

* **Pseudo-Assembly:**

  ```
  load &arr[0] -> r_base
  calc r = r_base + 1*sizeof(T)
  load [r] -> value
  ```
* **ASCII:**

```
[e0][e1][e2]
     ^ arr+1
```

* **Test:** `arr[1]` ve `*(arr+1)` aynı hücreye erişir.
* **Özet:** Decay = `T[]` → `T*`, boyut bilgisi kaybolur, indeksleme pointer aritmetiği ile yapılır.

---

## 3) Fonksiyon Parametresi: `T arr[]` ≈ `T*`

* **Kavram:** Fonksiyon parametresi olarak `T arr[]` yazmak sadece sözdizimsel şeker; tip `T*` olur.
* **Mini Senaryo:**

```c
void f(int a[]) { a[0]=5; }
void g() {
    int arr[3];
    f(arr); // arr → &arr[0]
}
```

* **Pseudo-Assembly:**

```
load &arr[0] -> arg
call f(arg)
```

* **ASCII:**

```
[caller] arr -> base
[callee] a -> base
```

* **Derleyici Notu:** `sizeof(a)` fonksiyon içinde pointer boyutunu verir.
* **Özet:** Parametre `T*`’tır, boyut bilgisi taşınmaz, indeksleme yine offset ile yapılır.

---

## 4) Pointer Aritmetiği ve Aliasing

* **Kavram:** Pointer aritmetiği, `p+i` = adres + `i*sizeof(T)`.
* **Mini Senaryo:**

```c
int arr[4];
int *p = arr;
int *q = p + 2; // üçüncü elemana gider
```

* **Pseudo-Assembly:**

```
load p -> r1
calc r2 = r1 + 2*sizeof(T)
load [r2] -> v
```

* **ASCII:**

```
[e0][e1][e2][e3]
 ^    ^
 p    q
```

* **Derleyici Notu:** Strict aliasing kuralı ihlali UB; `restrict` anahtar kelimesi alias yok garantisi verir.
* **Özet:** Aritmetik eleman boyutuna göre, aliasing optimizasyonunu etkiler.

---

## 5) String Literal vs Array (Kritik!)

* **Kavramlar:**

  * `char *p = "merhaba"` → read-only literal pointer, değiştirilemez.
  * `char arr[] = "merhaba"` → mutable stack kopyası, değiştirilebilir.
  *  Unutma stackte de olsa string literaller önce rodata da oluşturulur 
  ardından stacke kopyalanır.
* **Mini Senaryo:**

```c
*p = 'M';   // UB, crash
arr[0] = 'M'; // güvenli
```

* **Pseudo-Assembly:**

```
Pointer: load .rodata_addr -> p; [p] read-only
Array:   memcpy .rodata -> stack; [stack+offset] writable
```

* **ASCII:**

```
Pointer yaklaşımı:
.rodata: ["merhaba\0"]
Stack:   [p] -> .rodata addr

Array yaklaşımı:
.rodata: ["merhaba\0"]
Stack:   [m][e][r][h][a][b][a][\0]
```

* **Özet Tablo:**

```
| Kullanım        | Depolama       | Değiştirilebilir? | Kopya var mı? |
|-----------------|----------------|------------------|---------------|
| char arr[] = "" | Stack/Data     | Evet             | Evet          |
| char *p = ""    | .rodata        | Hayır (UB)       | Hayır         |
```

---

## 6) Heap-Tabanlı Dinamik Matris

* **Pointer to array:** `int (*data)[5] = malloc(4 * sizeof *data);`

  * `*data` tipi: `int[5]` (tek satır)
  * `sizeof *data = 5*sizeof(int)`
  * 4 ile çarpınca → 4×5 int’lik contiguous blok

* **Erişim:** `data[i][j]` → i’inci satır, j’inci sütun

* **Stack vs Heap:**

  * Stack array: `int arr[4][5];` compile-time sabit, otomatik cleanup
  * Heap array: runtime boyut, manuel `free`

* **Array of pointers vs pointer to array:**

```c
// 1. Array of pointers (satırlar ayrı, non-contiguous)
int **arr = malloc(4 * sizeof(int*));
for (int i=0;i<4;i++) arr[i] = malloc(5*sizeof(int));

// 2. Pointer to array (tek blok, contiguous)
int (*arr)[5] = malloc(4 * sizeof *arr);
```

* **Özet Tablo:**

```
| Yöntem          | Bellek               | Satırlar  | Free            | Notlar                      |
|-----------------|--------------------|----------|----------------|-----------------------------|
| int **arr       | Heap, non-contiguous| Ayrı blok| Her satır + arr | Esnek, pointer overhead     |
| int (*arr)[5]   | Heap, contiguous    | Tek blok | Tek free        | Performans iyi, sütun sabit |
| int arr[4][5]   | Stack, contiguous   | Tek blok | Otomatik        | Compile-time sabit          |
```

---

## 7) Glossary / Terimler

* **Array label:** Array için linker/derleyici tarafından atanmış taban adres
* **Decay:** `T[]` → `T*`, boyut bilgisi kaybı
* **Strict aliasing:** Farklı tür pointer’ların aynı bellek üzerinde aynı anda kullanılması optimizasyon hatası oluşturabilir
* **restrict:** Alias yok garantisi verir (C99)
* **SP-based addressing:** Stack pointer tabanlı adresleme
* **String literal:** Read-only segment’te saklanan sabit metin
* **.rodata:** Read-only data segment

---

## 8) Önemli Notlar / Testler

* `sizeof(arr)` → pointer mı yoksa array boyutu mu? Parametre vs local değişken farkı var
* `arr[i]` = satır başlangıcı, `arr[i][j]` = element
* Pointer arithmetic ile array indexlenebilir
* Literal ile array farkına dikkat: mutable / read-only

---
