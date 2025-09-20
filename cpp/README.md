# C++ — Referans Semantik ve String/Vector Hafıza Modeli (Şematik Rehber)

## 1️⃣ Referans = Alias

* **Kavram:** `int& a = x;` → `a`, `x` ile aynı adrestir; yeniden bağlanamaz, null olamaz.
* **Mini Senaryo:** `a` üzerinden yazmak `x`’i değiştirir.
* **Pseudo-Assembly:**

  ```
  load &x -> r
  alias r as &a
  store 20 -> [r]
  ```
* **ASCII Bellek Temsili:**

  ```
  [stack] x:10 <===> a
  ```
* **Özet:**

  * Referans = takma ad.
  * Yeniden bağlanamaz, null olamaz.
  * Derleyici optimize edebilir: isim yerine doğrudan adres kullanılabilir.

---

## 2️⃣ Referans vs Pointer

* **Kavram:**

  * Referans → doğal sözdizim, sabit.
  * Pointer → adres taşıyıcı, yeniden atanabilir, null olabilir.
* **Mini Senaryo:**

  ```cpp
  int x=5; 
  int* p = &x; 
  int& a = x;
  ```
* **Pseudo-Assembly:**

  ```
  load &x -> r
  store r -> [p]
  alias r as &a
  load [p] -> r2; load [r2] -> v   ; pointer dolaylı
  load [r] -> v2                   ; referans doğrudan
  ```
* **ASCII:**

  ```
  [stack] x <===> a,  p -> (&x)
  ```
* **Özet:**

  * Ref = alias; Ptr = adres.
  * Ref sabit; Ptr değişken.
  * Optimize: ref genelde daha hızlı ve şeffaf.

---

## 3️⃣ Parametre Geçişi (Value / Reference / Const&)

* **Küçük POD türleri:** `value` ucuz, register’da tutulabilir.
* **Büyük türler:** `const&` tercih edilir; kopyasız, güvenli.
* **Mini Senaryo:**

  ```cpp
  void inc(int& a);
  int sum(int a, int b);
  ```
* **Pseudo-Assembly:**

  ```
  alias &x as &arg0       ; reference param
  store y -> [arg1_copy]  ; value param
  call inc(arg0)
  call sum(arg1_copy,1)
  ```
* **ASCII:**

  ```
  [caller] x:10,y:7
  [callee inc] a => x
  [callee sum] a:7 (kopya)
  ```
* **Özet:**

  * `inc` → dışarıyı değiştirir.
  * `sum` → kopya, dışarıyı değiştirmez.
  * Küçük tür → value; büyük tür → const&.

---

## 4️⃣ Hibrit Nesneler: string / vector

* **Kavram:** Header (size/cap/data) stack’te, veri buffer’ı çoğu zaman heap’te.
* **Mini Senaryo:** `vector` büyüdüğünde yeni heap buffer alır.
* **Pseudo-Assembly:**

  ```
  store {size,cap,data=null} -> [hdr]
  call heap_alloc(...) -> r
  store r -> [hdr.data]
  ```
* **ASCII Bellek:**

  ```
  [stack] hdr{size,cap,data->@B}
  [heap]  @B: [ .... ]
  ```
* **Özet:**

  * Header → stack
  * Veri → heap
  * Move, copy elision → ucuz

---

## 5️⃣ std::string ve SSO (Small String Optimization)

* **Kısa metinler (SSO):** header içinde, stack.
* **Uzun metinler:** heap buffer.
* **Mini Senaryo:**

  ```cpp
  std::string s1 = "ok";                 // SSO
  std::string s2 = "hello world this is long"; // heap
  ```
* **Pseudo-Assembly:**

  ```
  store {size=2, inline="ok"} -> [hdr]
  store {size=18, data->@C} -> [hdr]
  ```
* **ASCII Bellek:**

  ```
  [stack] hdr(SSO) inline:'o','k'
          hdr(heap) data->@C
  [heap]  @C: 'h','e','l','l','o',' ','w','o',...
  ```
* **Özet:**

  * Kısa → inline, heap yok
  * Uzun → heap allocate
  * Move / copy elision hızlı

---

## 6️⃣ std::string Arka Plan Modeli (Konsept)

* Header = `{ size, capacity, data }`
* `data` çoğu implementasyonda char buffer pointer’ı gibi davranır, gerçek pointer değişkeni yaratılmaz.
* **Test:**

  * `"hello"` → heap yok, SSO
  * `"hello world..."` → heap var
* **Özet:**

  * Header → stack
  * Kısa → inline, uzun → heap
  * `data` → conceptual char\* pointer
  * Implementasyon farklılıkları olabilir

---

## 🔑 Terimler

* **Alias:** Aynı adresi paylaşan farklı isim
* **Reference elimination:** Referans isminin kaldırılıp doğrudan asılın kullanılması
* **POD:** Plain Old Data; küçük sabit boyutlu türler
* **Header (string/vector):** size/capacity/data meta alanı
* **SSO:** Small String Optimization; kısa metnin başlıkta tutulması

---

