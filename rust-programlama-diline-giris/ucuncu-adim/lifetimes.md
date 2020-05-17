# Yaşam süreleri
Referanslarla çalışırken onları kullanmayı bırakana kadar referans verisinin canlı kaldığından emin olmalıyız. 

Şöyle ki; 
  - Bir değerle bağladığımız 'a' adında bir değişkenimiz var.
  - Bu `a` değişkenini `x` adındaki başka bir değişkene referans toluyla bağladığımız takdirde `x` değişkeniyle işimiz bitene kadar `a` değişkeninin yaşadığından emin olmamız gerekir. 
  
>🔎 Bellek yönetimi, bilgisayar belleğine uygulanan bir kaynak yönetimi şeklidir. Programlama dillerinin çoğu 1990'ların ortasına kadar, programcının kullanılmayan nesneleri tanımlamak veya serbest bırakabilmesi için talimatlar vermesini gerektiren **Elle Bellek Yönetimi**'ni kullanıyordu. 1959'da John McCarthy, bir **Otomatik Bellek Yönetimi** (AMM) şekli olan **Çöp Toplayıcı** sistemini *(GC)* icat etti. Bu sistem programlayıcıya güvenmek yerine hangi belleğin artık kullanılmayacağını belirler ve o belleği otomatik olarak serbest bırakır. **Objective-C ve Swift** dilleri de, **Otomatik Referans Sayma** (ARC) adı verilen benzer bir işilevsellik sunarlar.

## Yaşam Süresi Nedir?
Rust dilinde,

  * Aynı anda bir kaynağın yalnızca **tek sahibi** bulunur. Bu durum **kapsam dışına çıkıldığında** biter ve kaynak bellekten kaldırılır.
  * Aynı kaynağı tekrar kullanmak istediğimizde onu içeriğinden **referans** yoluyla **ödünç** alıyoruz.
  * **Referanslar** ile çalışırken **derleyicinin** başvurulan kaynakları **ne zamana kadar** kullanılabileceğini anlayabilmesi için bu kaynakların **yaşam sürelerini** bildiren **ek talimatlar** verilmesi gerekir. 
  * ⭐ Ancak bu ek talimatlar **kodu daha ayrıntılı** hale getireceğinden **ortak kalıpları** daha ergonomik hale getirmek için yaşam sürelerinin `fn` tanımlarında **elenmesine veya ihmal edilmesine** izin verilir. Bu durumda, derleyici ömür boyu ek açıklamaları dolaylı olarak atar.

Yaşam süreleri ek açıklamaları **derleme zamanında** denetlenir. Hafıza ise, verinin ilk ve son olarak ne zaman kullanıldığı derleyici tarafından kontrol edildikten sonra **çalışma zamanında** yönetilir. Rust derlemelerinin yavaş olma nedeni budur.

> * C ve C++ 'dan farklı olarak, Rust **genelde** değerleri açıkça düşürmez.
> * GC'den farklı olarak Rust, referans alınmayan veriler için hafızadan yer ayırma çağrısında bulunmaz.
> * Rust, kapsam dışına çıkmak üzere olan verilerin bellek ayırma çağrılarını değerlendirerek bu kaynağa daha fazla başvuru yapılmasını engeller.

## Kullanım
Yaşam süreleri bir **kesme `'`** işareti ile gösterilir ve kurallara göre adlandırılırken küçük harf kullanılır. Bu kullanım genellikle `'a` ile başlar ve **çok sayıda yaşam süresi** belirtilmesi gerektiğinde sırasıyla **alfabenin diğer harfleri** kullanılır. 

Referansları kullanılırken, aşağıdaki başlıklarda gösterilen kurallara riayet edilmelidir.

### 01. İşlevlerle
  * Referanslı giriş ve çıkış parametreleri kullanıldığında **`&`** işaretinden sonra parametrelerin yaşam süreleri bildirilmelidir.
  Örneğin: `..(x: &'a str)` , `..(x: &'a mut str)`
  * Genelleştirilen işlevlerde yaşam sürelerinin genellenen türler için olduğu, işlev adından sonra bildirilmelidir.
    Örneğin: `fn foo<'a>(..)` , `fn foo<'a, 'b>(..)`

```Rust
// Giriş parametresi yok, referans döndürür
fn bir_islev<'a>() -> &'a str   {...}

// // Bir giriş parametresi
fn bir_islev<'a>(x: &'a str)    {...}

// Bir giriş bir çıkış parametresi var
// Her ikisi de aynı yaşam süresine sahip 
// En azından giriş var olduğu sürece ÇIKIŞ devam etmelidir
fn bir_islev<'a>(x: 'a &str) -> &'a str {...}

// Çok sayıda giriş parametresinde sadece 
// bir giriş ve çıkış aynı yaşam süresini paylaşır 
// En azından y girişi olduğu sürece ÇIKIŞ var olmalı ve yaşamalıdır.
fn bir_islev<'a>(x: i32, y: &'a str) -> &'a str {...}

// Çoklu girişlerde her biri için belirtilmişse
// hem giriş hem çıkış aynı yaşam süresini kullanır. 
// En azından x ve y girişi olduğu sürece ÇIKIŞ var olmalı ve yaşamalıdır. 
fn bir_islev<'a>(x: &'a str, y: &'a str) -> &'a str {...} 

// Bu tür çoklu girişlerde girişlerin farklı yaşam süreleri olabilir 🔎
// En azından x olduğu sürece ÇIKIŞ var olmalı ve yaşamalıdır. 
fn bir_islev<'a, 'b>(x: &'a str, y: &'b str) -> &'a str {...} 
````

### 02. Yapı veya Enum Türleriyle
  * Referanslı elemanların yaşam süreleri **`&`** işaretinden hemen sonra bildirilmelidir.
  * `struct` veya `enum` adından sonra, verilen yaşam sürelerinin genellenen türler olduğunu bildirmek zorunludur.
  
```Rust
// Tek öğe 
// x'in verisi yaşamını BirYapıdan çıkılana kadar sürdürmelidir.
struct BirYapi<'a> { 
    x: &'a str 
} 

// Çok sayıda öğe 
// x ve y’nin verisi yaşamını BirYapi çıkılana kadar sürdürmelidir. 
struct BirYapi<'a> { 
    x: &'a str, 
    y: &'a str 
} 

// Bir varyantlı enum türü 
// varyanta ait veri yaşamını Enum’dan çıkılana kadar sürdürmelidir. 
enum Sirala<'a> { 
    Varyant(&'a Type) 
}
````
### 03. Uygulama ve Özelliklerle
```Rust
struct BirYapi<'a> {
    x: &'a str
}
    impl<'a> BirYapi<'a> {
        fn function<'a>(&self) -> &'a str {
            self.x
        }
    }


struct BirYapi<'a> {
    x: &'a str,
    y: &'a str
}
    impl<'a> BirYapi<'a> {
        // impl bildiriminde <'a> bulunduğundan new için yeniden belirtmeye gerek yok.
        fn new(x: &'a str, y: &'a str) -> BirYapi<'a> { 
              BirYapi {
              x : x,
              y : y
          }
        }
    }


// 🔎
impl<'a> Trait<'a> for Type
impl<'a> Trait for Type<'a>
````

### 04. Genellenen Türlerle
```rust
// 🔎
fn bir_islev<F>(f: F) where for<'a> F: FnOnce(&'a Type)
struct BirYapi<F> where for<'a> F: FnOnce(&'a Type) { x: F }
enum Siralama<F> where for<'a> F: FnOnce(&'a Type) { Variant(F) }
impl<F> BirYapi<F> where for<'a> F: FnOnce(&'a Type) { fn x(&self) -> &F { &self.x } }
```

## Yaşam süresi seçimi
Daha önce de belirttiğim gibi, **ortak kalıpları** daha ergonomik hale getirmek için Rust, yaşam sürelerinin **kaldırılmasını/atlanmasını** sağlar. Bu işleme **Yaşam süresi seçimi** adı verilir.

💡 Rust Şu an için yalnızca `fn` tanımlarında *Yaşam süresi seçimlerini* desteklemektedir. bununla birlikte gelecekte, `impl` başlıkları da desteklenecektir.

`fn` tanımlarının yaşam süresi ek açıklamaları **Parametre listesinde** aşağıdakilerin her ikisi bulunuyorsa derleyici tarafından seçilebilir.
  
  * Yalnızca bir giriş parametresi referans ile iletiliyorsa
  * Parametre `&self` ya da `&mut self` referansı taşıyorsa
