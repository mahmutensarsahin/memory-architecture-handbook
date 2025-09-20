# compiler-reference-semantics

C, C++ ve Java’da “referans semantikleri”ni kısa, şematik ve derleyici bakışıyla anlatan mini başvuru. Amaç: aliasing, parametre geçişi, bellek yerleşimi (stack/heap) ve derleyici optimizasyonlarının sezgisini kazandırmak.

 - Biçim: yalın pseudo-assembly, ASCII diyagramlar. Gerçek kod/IO yok.

## Cpp ile reference farkı
- Java: Nesneler heap’te; değişkenler referans (adres değeri) taşır; null olabilir; ömrü GC yönetir.
- `MyClass a = new MyClass();` → stack: `a -> 0xABCD` (heap nesnesi)
- `MyClass b = a;` → `b` aynı nesneyi gösterir; alan değişikliği her iki isimden de görünür.
- Java referansı “pointer benzeri”dir ama pointer aritmetiği yoktur; yeniden atanabilir: `b = new MyClass()`.
- C++ referansı isim düzeyinde alias’tır: `int& r = x;` → `r` sadece `x`’i temsil eder; null olamaz, yeniden bağlanamaz; scope sonunda hükmü biter.
- Kısa özet: Java = esnek, GC destekli adres; C++ = sabit, alias ve optimize edilebilir.
- C’de dizi adı (etiket): `arr` çoğu bağlamda taban adresini ifade eder (decay: `T[] -> T*`). İsim/etiket benzetmesi, C++ referansının “isim olarak aynı yeri göstermesi” fikrine yakındır.
- Fonksiyon parametreleri (C++): `T` → kopya; `T&` → alias (yan etki görünür); `const T&` → kopyasız okuma; `T*` → açık adres. Derleyici, parametre türünden çağrı protokolünü seçer.
- Lvalue/Rvalue vurgusu: Lvalue adreslenebilir varlık; rvalue geçici. `T&&` geçiciyi bağlar (yeniden bağlama yok). Alias ve yan etkilerin görünürlüğü, lvalue/rvalue bağlamına bağlıdır.

## Nasıl Okunur?
1. Bu dosyadaki ortak kavramlara hızlıca göz at.
2. `cpp/`, `c/`, `java/` klasörlerindeki bölümleri sırayla oku.
3. `diagrams/` içindeki ASCII/PNG yer tutucularına bak.

## Dosya Haritası
- `cpp/README.md`: C++ referansları, parametre geçişi, `std::string`/`std::vector`, SSO.
- `c/README.md`: C dizileri, array-to-pointer decay, pointer aritmetiği.
- `java/README.md`: Nesne referansları, pass-by-value of reference, GC etkisi, String.
- `diagrams/README.md`: ASCII diyagramlar ve PNG yer tutucuları listesi.
- `tests/TEST_SCENARIOS.md`: Kod yazmadan gözlem/akıl yürütme senaryoları.
- `glossary.md`: Kısa terim sözlüğü (Türkçe — İngilizce).

## Sözleşmeler ve Notasyon
- Pseudo-assembly (insan-okur):
  - `load x -> r1`, `store r1 -> [addr]`, `call f(a, b)`, `ret v`
  - Yığın (stack) hareketi sözle: “SP düşer: yer açılır”, “SP yükselir: yer kapanır”.
- ASCII diyagram stili:
  - `[stack frame]`, `[heap block]`, oklar: `->` (işaret/alias), `=>` (referans ilişki benzetimi)
- Kısıtlar:
  - Kod dosyası yok, gerçek assembly/IR dumps yok, sadece şematik anlatım.
- Terminoloji Notu (kısa):
  - “Mantıksal (konseptüel) pointer”: Gerçek, bağımsız bir pointer değişkeni olmasa da bir başlıktaki `data` alanı gibi, heap’teki bir buffer’ı işaret eden char* benzeri göstergeyi ifade eder (örn. `std::string`’de SSO hariç durumda `data`).

---

## Diagram Önerileri - Kod Analizi Sonuçları

Aşağıdaki C, C++ ve Java kod dosyalarını analiz ettim. Kodlarda öne çıkan veri yapıları, bellek kullanımı, referans/pointer ilişkileri, nesne yaşam döngüleri ve sistem iş akışlarını tespit ederek şu diagram listesini öneriyorum:

### 1. C Karmaşık Pointer Declarator Parse Tree Diagramı
**İçerik:** c-advanced-arrays/README.md'deki karmaşık pointer declarator'ların (`int (*(*p)[4])[5]` gibi) sistematik okuma algoritması  
**İlişkiler:** Right-left rule adımları, parantez önceliği, tip çözümleme süreci  
**Dikkat noktaları:** Identifier→pointer→array→element zincirleme çözümlemesi

### 2. C Stack vs Heap 2D Array Memory Layout Karşılaştırması
**İçerik:** c-advanced-arrays/README.md'deki array-of-pointers, pointer-to-array, stack array yaklaşımları  
**İlişkiler:** Bellek parçalanması, cache locality, malloc/free patterns  
**Dikkat noktaları:** Contiguous vs non-contiguous memory, performance implications

### 3. C String Literal vs Array Memory Segmentation Diagramı  
**İçerik:** c-string-examples/README.md'deki .rodata vs stack farkı  
**İlişkiler:** String literal storage, stack copy mechanism, write protection  
**Dikkat noktaları:** UB risks, memory corruption, segmentation fault scenarios

### 4. C++ Temporary Object Lifetime Extension Flowchart
**İçerik:** cpp-temporaries/README.md'deki const referans ile geçici nesne ömür uzatma  
**İlişkiler:** RVO optimization, copy elision, destructor timing  
**Dikkat noktaları:** Dangling reference prevention, scope-based lifetime management

### 5. C++ String SSO vs Heap Mode State Transition Diagramı
**İçerik:** cpp/README.md ve mevcut SSO diagramları  
**İlişkiler:** Small String Optimization threshold, heap allocation triggers, capacity growth  
**Dikkat noktaları:** Iterator invalidation, reallocation boundaries, memory efficiency

### 6. C++ Reference vs Pointer Semantic Comparison Chart
**İçerik:** cpp/README.md'deki alias semantics vs address semantics  
**İlişkiler:** Type safety, null safety, rebinding capabilities, optimization implications  
**Dikkat noktaları:** Reference elimination, compiler optimizations, performance characteristics

### 7. Java Object Reference Pass-by-Value Mechanism Diagram
**İçerik:** java/README.md'deki pass-by-value of reference kavramı  
**İlişkiler:** Field mutation visibility, reference reassignment invisibility, heap object identity  
**Dikkat noktaları:** Object identity vs equality, GC implications, thread safety

### 8. Java Memory Model Thread Synchronization Flowchart
**İçerik:** java/README.md'deki JMM, publish/acquire semantics  
**İlişkiler:** Escape analysis, scalar replacement, JIT optimizations  
**Dikkat noktaları:** Memory barriers, visibility guarantees, happens-before relationships

### 9. Cross-Language Memory Management Comparison Matrix
**İçerik:** Tüm dillerdeki stack/heap patterns  
**İlişkiler:** Automatic vs manual memory management, GC vs RAII, ownership semantics  
**Dikkat noktaları:** Performance trade-offs, safety guarantees, programmer responsibility

### 10. Compiler Optimization Impact Visualization
**İçerik:** Tüm dosyalardaki compiler optimization mentions  
**İlişkiler:** Register allocation, alias analysis, dead code elimination, inlining  
**Dikkat noktaları:** Debug vs release behavior, undefined behavior exploitation, profile-guided optimization

### 11. Function Parameter Semantics Decision Tree
**İçerik:** C/C++/Java'daki farklı parameter passing patterns  
**İlişkiler:** Copy cost, reference semantics, const correctness, move semantics  
**Dikkat noktaları:** Performance implications, semantic differences, best practice guidelines

### 12. Memory Layout Architecture Diagram
**İçerik:** Tüm dillerdeki .text, .rodata, .data, .bss, stack, heap segments  
**İlişkiler:** Linker behavior, runtime layout, protection mechanisms  
**Dikkat noktaları:** Address space layout, memory protection, segmentation faults

Bu diagram önerileri, mevcut kod dosyalarındaki teknik derinlik ve eğitim amaçlı içeriği en iyi şekilde görselleştirecek şekilde tasarlanmıştır.

---
- C:
  - Array-to-pointer decay: `arr` → `&arr[0]` (T*), boyut kaybı.
  - Parametre `T arr[]` ≈ `T*`; `sizeof(arr)` tanımda toplam, paramda pointer boyutu.
  - İndeksleme = `*(p + i)`; adres kaydırma `i*sizeof(T)`.
- C++:
  - Referans = alias; yeniden bağlanmaz, null olamaz. Ref vs Ptr: ref şeffaf, ptr yeniden atanabilir.
  - Parametre: value (kopya), reference (alias), `const&` (kopyasız, değiştirilemez).
  - `std::string`: Header stack’te; kısa metin SSO (inline), uzun metin heap (header.data = mantıksal char*).
- Java:
  - Nesneler heap’te; değişkenler referans tutar.
  - “Pass-by-value of reference”: Metoda referansın kopyası geçer; alan mutasyonu görünür, yeniden atama görünmez.
  - String immutable; yeni içerik = yeni nesne. JIT/GC görünürlük kuralları JMM’ye tabidir.

---

## Ortak Kavram: Stack vs Heap
1) Kavram:
- Stack (yığın): Fonksiyon çağrılarıyla büyüyüp küçülen, yerel değişkenlerin tipik olarak tutulduğu alan.
- Heap (öbek): Dinamik ömürlü nesnelerin yaşadığı alan; yönetimi programın/çalışma zamanının elinde.

2) Mini Senaryo:
- Bir fonksiyon bir tamsayıyı yerelde oluşturur; sonra heap’te küçük bir blok ayırır.

3) Pseudo-Assembly:
- SP düşer: `x` için yer açılır
- `store 10 -> [x]`
- `call heap_alloc(size=16) -> r1`
- `store r1 -> [p]`  ; p: heap blok adresini tutan yerel
- SP yükselir: dönüşte frame kapanır

4) ASCII Diyagram:
```
[stack]
  [caller frame]
  [callee frame]
    x: 10
    p: -> [heap block @0xHH]

[heap]
  [0xHH: 16 byte block]
```

5) Derleyici Notu:
- Yerel sabitler register’da tutulabilir; `x` hiç belleğe yazılmayabilir (register tahsisi).
- `heap_alloc` çağrısı kaçınılmazsa da, gereksiz yükleme/saklama adımları sadeleşebilir.

6) Test Senaryosu:
- Adım: Diyagramı izle; `x` stack’te, `p` stack’te ama işaret ettiği veri heap’te.
- Beklenen: Frame kapanınca `x` ve `p` kaybolur; heap bloğu yaşamaya devam eder (serbest bırakılmadıkça/GC edilmedikçe).
- Not: Optimize derlemelerde `x`’in açık bir stack yuvası görünmeyebilir.

7) Özet:
- Stack geçici; fonksiyon yaşam döngüsüne bağlı.
- Heap dinamik; adresi `p` gibi değişkenler taşır.
- Derleyici, uygun gördüğünde yerelleri register’a alır.

---

## Ortak Kavram: Lvalue/Rvalue ve Aliasing
1) Kavram:
- Lvalue: Adresi alınabilir varlık; rvalue: geçici değer.
- Aliasing: Aynı belleği birden çok isimden görmek; referanslar/pointer’lar alias oluşturur.

2) Mini Senaryo:
- `a` isimli referans, `x`’in takma adı (alias) olsun.

3) Pseudo-Assembly:
- `load &x -> r1`
- `alias r1 as &a`  ; a, x ile aynı adresi temsil eder
- `load [r1] -> r2` ; a veya x fark etmez, aynı hücre
- `store 42 -> [r1]`

4) ASCII Diyagram:
```
[stack]
  x: 10  <===>  a (alias)
```

5) Derleyici Notu:
- Basit durumlarda “reference elimination”: `a` görüldüğü yerde `x` kullanılabilir.
- Alias analizi güçlü ise gereksiz yükleme/saklama kaldırılır.

6) Test Senaryosu:
- Adım: Diyagramda `a` ve `x`’in aynı hücreye baktığını doğrula.
- Beklenen: `x` değişince `a` “değişmiş gibi” görünür: tek hücre.
- Not: Optimize derlemelerde `a` ismi kaybolabilir; tek sembolik adres kalır.

7) Özet:
- Lvalue: adreslenebilir; rvalue: geçici.
- Alias: aynı adres, farklı isim.
- Derleyici alias’ı çözerse kopyaları kaldırır.

---

## Sonraki Adım
- C++: Referanslar, parametre semantiği, `std::string`/`std::vector` ve SSO → `cpp/README.md`
- C: Array decay, pointer aritmetiği → `c/README.md`
- Java: Nesne referansları, pass-by-value of reference, GC → `java/README.md`