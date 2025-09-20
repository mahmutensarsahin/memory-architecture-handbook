# Java — Referans Semantiği (Gözle Görsel ve Şematik)

## 1️⃣ Nesneler Heap’te, Değişkenler Referans Tutar

* **Kavram:** Java’da değişkenler nesnelere **doğrudan sahip değil**, **referans (adres)** tutar. Nesneler **heap** üzerinde yaşar.

  * **Önemli:** Referans = pointer benzeri bir değişken.

* **Mini Senaryo:**

```java
Point p = new Point();
p.x = 5;
```

* **Pseudo-Assembly (okur formatı):**

```
call new_object(Point) -> r1
store r1 -> [p]        ; p stack'te (referans)
load [p] -> r2
store 5 -> [r2.x]      ; heap'teki nesneye yaz
```

* **ASCII Şeması:**

```
[stack] p -> @0xDD  (referans)
[heap]  @0xDD: { x: 5, y: 0 }
```

* **Notlar:**

  * JIT derleyici kaçış analizi yaparsa, nesneyi register’da saklayabilir (scalar replacement).
  * Referansın üzerinden alan değişimi **doğrudan görünür**.

---

## 2️⃣ Pass-by-Value of Reference

* **Kavram:** Java’da **her parametre by-value** geçer.

  * Nesne parametresinde **değer = referansın kopyası**dır.
* **Mini Senaryo:**

```java
void setX(Point p) { p.x = 7; }
void reassign(Point p) { p = new Point(); }
```

* **Pseudo-Assembly:**

```
load [caller_p] -> r1
call setX(r1)       ; x değişir
call reassign(r1)   ; sadece kopya değişti, caller etkilenmez
```

* **ASCII Şeması:**

```
[caller] p -> @0xAA
[callee setX] ref(copy) -> @0xAA  ==> x yazılır (heap değişir)
[callee reassign] ref(copy) -> @0xAA --(içeride)-> @0xEE (kopya değişti, caller p aynı)
```

* **Test Çıktısı:**

```java
Point p = new Point();
setX(p);        // p.x = 7 görünür
reassign(p);    // p hala eski nesneye işaret eder
```

* **Özet:**

  * Alan mutasyonu görünür.
  * Referansın yeniden ataması görünmez.

---

## 3️⃣ JVM Bellek Modeli ve Garbage Collector (GC)

* **Kavram:**

  * Java Memory Model (JMM): Görünürlük ve sıralama kuralları.
  * GC: Nesnelerin ömrünü yönetir.

* **Mini Senaryo (Thread & Publish/Acquire):**

```
T1 yayınlar -> T2 okur; senkronizasyon ile tutarlılık sağlanır.
```

* **Pseudo-Assembly:**

```
call new_object(Foo) -> r
publish r (release)
acquire r in other thread
```

* **ASCII Şeması:**

```
[T1] new -> publish   ||  [T2] acquire -> use
[heap] young -> (GC) -> old
```

* **Notlar:**

  * Kaçış etmeyen nesneler register’da tutulabilir (scalar replacement).
  * JMM: Thread’ler arası görünürlük ve sıralama garantisi verir.

---

## 4️⃣ String İmmutability

* **Kavram:** `String` nesneleri **immutable**; değişiklik = yeni nesne.

* **Mini Senaryo:**

```java
String s = "hi";
String t = s.concat("x");
```

* **Pseudo-Assembly:**

```
load s -> r1
call concat(r1, "x") -> r2
ret r1   ; orijinal değişmedi
```

* **ASCII Şeması:**

```
[stack] s -> @S1 ("hi")
[heap]  @S1: "hi" (immutable)
        @S2: "hix" (yeni nesne)
```

* **Notlar:**

  * String interning: Aynı sabitler paylaşılır.
  * JIT: Sabit katlama ile tahsisleri optimize eder.

---

## 5️⃣ Özet Tablo

| Konsept         | Örnek           | Bellek                | Ömür                    | Görünürlük                                     |
| --------------- | --------------- | --------------------- | ----------------------- | ---------------------------------------------- |
| Nesne referansı | `Point p`       | Stack: p, Heap: nesne | Stack frame boyunca     | Alan değişimi görünür                          |
| Pass-by-value   | `setX(Point p)` | Parametre kopyası     | Fonksiyon scope         | Alan mutasyonu görünür, yeniden atama görünmez |
| GC & JMM        | `new Foo()`     | Heap                  | GC tarafından yönetilir | Thread senkronizasyonu önemli                  |
| String          | `"hi"`          | Heap + stack referans | Immutable               | Yeni içerik = yeni nesne                       |

---

## Terimler (Glossary)

* **Object reference:** Nesne adresini tutan değişken (pointer benzeri).
* **Pass-by-value of reference:** Referansın kopyasıyla parametre geçişi.
* **Escape analysis:** Nesnenin scope dışına çıkıp çıkmadığını analiz eden JIT tekniği.
* **Scalar replacement:** Nesne alanlarının ayrı değişkenlere ayrılması, heap tahsisini önler.
* **JMM (Java Memory Model):** Çok-thread görünürlük ve sıralama kuralları.
* **Publish/acquire:** Yayınlama/edinme bariyerleri, thread güvenliği.
* **GC:** Garbage Collector; otomatik ömür yönetimi (young/old kuşak).
* **String interning:** Aynı sabit String’lerin paylaşımı.

---

✅ **Vurgu:**

* Referanslar, pointer benzeri değişkenlerdir; heap nesnelerine işaret eder.
* Pass-by-value of reference konsepti, alan değişimini görünür kılar ama yeniden atamayı gizler.
* JVM & GC mekanizması, görünürlük ve ömür yönetimini sağlar.
* String immutable, her değişiklik yeni nesne üretir.

---
