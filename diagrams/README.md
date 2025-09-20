# Diagrams

Bu klasörde compiler-reference-semantics projesi için oluşturulan Mermaid diyagramları yer almaktadır. Her diagram, C, C++ ve Java dillerindeki referans semantikleri, bellek yönetimi ve derleyici optimizasyonları konularını görsel olarak açıklamaktadır.

## Diagram Listesi

### 1. C Karmaşık Pointer Declarator Parse Tree (`1-c-complex-pointer-declarator.mmd`)
**İçerik:** Karmaşık pointer declarator'ların (`int (*(*p)[4])[5]` gibi) sistematik okuma algoritması  
**Teknoloji:** Mermaid Flowchart  
**Amaç:** Right-left rule adımlarını, parantez önceliğini ve tip çözümleme sürecini göstermek

### 2. C Stack vs Heap 2D Array Memory Layout (`2-c-stack-heap-2d-array-layout.mmd`)
**İçerik:** Array-of-pointers, pointer-to-array ve stack array yaklaşımlarının karşılaştırması  
**Teknoloji:** Mermaid Graph  
**Amaç:** Bellek parçalanması, cache locality ve performance implications'ı görselleştirmek

### 3. C String Literal vs Array Memory Segmentation (`3-c-string-literal-array-segmentation.mmd`)
**İçerik:** .rodata vs stack farkı, UB risks ve memory corruption scenarios  
**Teknoloji:** Mermaid Graph  
**Amaç:** String literal storage, write protection ve segmentation fault senaryolarını göstermek

### 4. C++ Temporary Object Lifetime Extension (`4-cpp-temporary-lifetime-extension.mmd`)
**İçerik:** Const referans ile geçici nesne ömür uzatma flowchart'ı  
**Teknoloji:** Mermaid Flowchart  
**Amaç:** RVO optimization, copy elision ve destructor timing'i açıklamak

### 5. C++ String SSO vs Heap Mode State Transition (`5-cpp-string-sso-heap-transition.mmd`)
**İçerik:** Small String Optimization threshold ve heap allocation triggers  
**Teknoloji:** Mermaid State Diagram  
**Amaç:** Iterator invalidation, reallocation boundaries ve memory efficiency'yi göstermek

### 6. C++ Reference vs Pointer Semantic Comparison (`6-cpp-reference-vs-pointer-comparison.mmd`)
**İçerik:** Alias semantics vs address semantics karşılaştırma chart'ı  
**Teknoloji:** Mermaid Graph  
**Amaç:** Type safety, null safety, rebinding capabilities ve optimization implications

### 7. Java Object Reference Pass-by-Value Mechanism (`7-java-object-reference-pass-by-value.mmd`)
**İçerik:** Field mutation visibility, reference reassignment invisibility diagramı  
**Teknoloji:** Mermaid Flowchart  
**Amaç:** Heap object identity, GC implications ve thread safety konularını açıklamak

### 8. Java Memory Model Thread Synchronization (`8-java-memory-model-thread-sync.mmd`)
**İçerik:** JMM, publish/acquire semantics ve memory barriers flowchart  
**Teknoloji:** Mermaid Flowchart  
**Amaç:** Escape analysis, scalar replacement ve JIT optimizations'ı göstermek

### 9. Cross-Language Memory Management Matrix (`9-cross-language-memory-management-matrix.mmd`)
**İçerik:** Automatic vs manual memory management, GC vs RAII karşılaştırması  
**Teknoloji:** Mermaid Graph  
**Amaç:** Performance trade-offs, safety guarantees ve programmer responsibility

### 10. Compiler Optimization Impact Visualization (`10-compiler-optimization-impact.mmd`)
**İçerik:** Register allocation, alias analysis, dead code elimination etkisi  
**Teknoloji:** Mermaid Graph  
**Amaç:** Debug vs release behavior, undefined behavior exploitation ve profile-guided optimization

### 11. Function Parameter Semantics Decision Tree (`11-function-parameter-semantics-decision-tree.mmd`)
**İçerik:** C/C++/Java parameter passing patterns karar ağacı  
**Teknoloji:** Mermaid Graph  
**Amaç:** Performance implications, semantic differences ve best practice guidelines

### 12. Memory Layout Architecture Diagram (`12-memory-layout-architecture.mmd`)
**İçerik:** .text, .rodata, .data, .bss, stack, heap segments mimarisi  
**Teknoloji:** Mermaid Graph  
**Amaç:** Linker behavior, runtime layout, protection mechanisms ve segmentation faults

## Mermaid Diagram Kullanımı

Bu diagramlar [Mermaid](https://mermaid.js.org/) formatında yazılmıştır. Görüntülemek için:

1. **GitHub'da:** Otomatik olarak render edilir
2. **VS Code'da:** Mermaid Preview extension'ı kullanın
3. **Web'de:** [Mermaid Live Editor](https://mermaid.live/) kullanın
4. **CLI'da:** `mmdc` tool'u ile PNG/SVG export yapın

## Diagram Kategorileri

### Bellek Yönetimi (Memory Management)
- Stack vs Heap Layout (2, 12)
- Memory Segmentation (3, 12)
- Cross-Language Memory Management (9)

### Dil Semantikleri (Language Semantics)  
- Pointer/Reference Semantics (1, 6, 7)
- Parameter Passing (7, 11)
- Object Lifetimes (4, 5)

### Derleyici Optimizasyonları (Compiler Optimizations)
- Optimization Impact (10)
- Memory Model (8)
- Performance Implications (2, 5, 6, 9, 11)

### Güvenlik ve Hata Yönetimi (Security & Error Handling)
- Memory Safety (3, 9, 12)
- Thread Safety (8)
- Undefined Behavior (3, 4, 10)

Bu diagramlar, compiler-reference-semantics projesinin eğitim materyalinin görsel kısmını oluşturur ve kodlardaki teknik derinliği görselleştirerek anlaşılabilirliği artırır.
