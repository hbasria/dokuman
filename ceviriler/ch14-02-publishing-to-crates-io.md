# Bir Sandığı Crates.io Üzerinde Yayınlamak
Önceki bölümlerde gerçekleştirdiğimiz bazı örnek projeler çalışabilmek için [crates.io](https://crates.io/)'daki bazı paketlere bağımlı olduklarından onları projelerimize dahil etmeyi öğrenmiştik. Halbuki sizler de kendi paketlerinizi yayınlayarak kodlarınızı başkalarıyla paylaşıma açabilirsiniz. [Crates.io](https://crates.io/)'daki sandık kayıt defteri, paketlerinizin kaynak kodunu dağıtacağından, öncelikle projenizin açık kaynak kodunu barındırmak zorundadır.

Rust ve Cargo, yayınlanan paketleri başkalarının da kolaylıkla bularak ve kullanabilmesini sağlayan özelliklere sahiptir. Bu özelliklerin bazılarından az sonra bahsedecekve ardından bir paketin nasıl yayınlanacağını anlatacağız.

## Kullanışlı Dökümantasyon Yorumları Oluşturmak
Paketlerinizi doğru bir şekilde belgelemek, diğer kullanıcıların bunları nasıl ve ne zaman kullanacaklarını bilmelerine yardımcı olacağından belge yazmak için harcadığınız zamana değecektir. Bölüm 3'te iki eğik çizgi `//` kullanarak Rust kodlarını nasıl yorumlayacağımızı tartışmıştık. Normal yorumların yanısıra Rust ayrıca dökümantasyon yorumları olarak bilinen ve kodlar içerisind eyapılan açıklamaları HTML belgelerine çevirmeye yarayan belirli bir yorumlama şekline de sahiptir. Bu HTML belgesi, sandığınızın nasıl uygulandığını anlatmaktan ziyade, sandığınızın nasıl kullanılacağını öğrenmek isteyen programcılara yol gösteren genel API öğelerinin Dokümantasyon yorumlarını gösterir. 

Belge yorumları iki yerine `///` üç eğik çizgi kullanır ve metni biçimlendirmek için Markdown gösterimini destekler. Doküman yorumlarını belgeledikleri öğeden hemen önce yerleştirin. Örnek 14-1, `my_crate` adlı sandıkta yer alan `add_one` işlevi için belge yorumlarını göstermektedir:

Dosya: src/lib.rs
```Rust
/// Kendisine iletilen sayıya bir ekler
///
/// # Örnekler
///
/// ```
/// let arg = 5;
/// let answer = my_crate::add_one(arg);
///
/// assert_eq!(6, answer);
/// ```
pub fn add_one(x: i32) -> i32 {
    x + 1
}
````
Örnek 14-1: Bir işlevin belgelenmesi

Bu örnekte, `add_one` işlevinin ne yaptığını açıklıyoruz, Örnekler başlığıyla bir bölüm başlatarak `add_one` işlevinin nasıl kullanılacağını gösteren kodları ekliyoruz. Ardından `cargo doc` komutunu çalıştırarak bu belge yorumlarından HTML belgesi oluşturabiliriz. Bu komut `rustdoc` araç setini çalıştırarak oluşturulan HTML belgelerini `target/doc` dizinine yerleştir.

Daha da kolayı `cargo doc --open` komutu hem mevcut sandığınız ve onun tüm bağımlılıklarının HTML belgelerini oluşturacak hem de oluşturduğu belgelerin web tarayıcınızda açılmasını sağlayacaktır. Şimdi `add_one` işlevine giderek, Örnek 14-1'de gösterilen belge yorumlarının metne nasıl dönüştürüldüğünü izleyebilirsiniz: 

![resim trpl14-01](https://github.com/rust-lang-tr/dokuman/blob/master/ceviriler/trpl14-01.png)
Şekil 14-1: `add_one` işlevinin HTML belgeleri

## Yaygın Olarak Kullanılan Bölümler
HTML belgesinde “Örnekler” şeklinde bir bölüm başlığı oluşturabilmek için Örnek 14-1'de `# Örnek` şeklinde işaretleme kullandık. Aşağıda ise, pek çok sandık yazarının belgelerinde yaygın olarak kullandıkları bazı bölümler verilmiştir:
  * **Panikler:** Belgelenen işlevin panik yapabileceği senaryolar. İşlevin çağrıldığı programlarda panik oluşması istenmiyorsa, kullanıcıların işlevi çağırmadıklarından emin olunmalıdır. 
  * **Hatalar** İşlev bir `Result` döndürürse, oluşabilecek hataların türleri ve bu hataların döndürülmesine neden olabilecek koşulların tanımlanması, işlevi çağıran tarafların farklı türdeki hataları farklı şekillerde işlemek için kod yazabilmeleri için yardımcı olabilir.
  * **Güvenlik** İşlevin çağrılması güvenli değilse (Bölüm 19'da güvensizliği tartışacağız), işlevin neden güvensiz olduğunu açıklayan ve işlevi çağıran tarafların desteklemesini beklediği sabitleri kapsayan bir bölüm olmalıdır.

Çoğu belgelendirme çalışmasında bu bölümlerin her birinin yorumlanmasına ihtiyaç duyulmaz, bununla birlikte bu bölümler size kodlarınızı çağıran tarafların bilmek isteyeceği yönlerini hatırlatmak için tavsiye edilen bir kontrol listesidir.

## Belgelendirme Yorumlarını Test Olarak Değerlendirmek 
Belge yorumlarınıza örnek kod blokları eklemek, kütüphanenizin nasıl kullanılacağını göstermenize yardımcı olabilir ve bunun ek bir avantajı da `cargo test` komutunu çalıştırarak dokümanlarınızdaki kod örneklerinin test edilmesi sağlanır! Hiçbir şey test edilebilecek örnekler içeren belgelerden daha iyi olamaz. Ancak, belgelendirme yapıldıktan sonra gerçekleşen kod değişiklikleri yüzünden işe yaramayan örneklerden daha kötü bir şey de yoktur. Örnek 14-1'deki `add_one` işlevi için gerçekleştirdiğimiz belgelendirmeye `cargo test` komutu uygularsak, aşağıdaki gibi bir test sonucu göreceğiz.

```bash
   Doc-tests my_crate

running 1 test
test src/lib.rs - add_one (line 5) ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
````
İşlev veya örnekteki `assert_eq!` değerini değiştirdiğimizde örnek panikler. Bu durumdayken `cargo test` komutunu yeniden  çalıştırılırsa belge testinde örnek ve kodun birbiriyle senkronize olmadığının tespit edildiğini gözlemleriz.

## İçerilen Öğelerin Yorumlanması
Bir başka belgelendirme biçimi olan `//!` ise yorum satırlarının hemen altına eklenenen öğeleri belgelemek yerine, yorumun ait olduğu, yani içerildiği öğeyi belgelendirirmek için kullanılır. Genellikle bu tarz yorum etiketlerini sandık veya modülün tamamını bir bütün olarak belgelemek amacıyla kök dosyasının içinde (kural gereği src/lib.src) ya da bir modül içerisinde kullanırız. 

Örneğin `add_one` işlevini içeren `my_crate` adlı sandığın amacını açıklayan belgeler eklemek istiyorsak src/lib.rs dosyasının en başına örnek 14-2' de gösterileceği gibi  `//!` işaretini belge yorumu olarak ekleyebiliriz.

Dosya: src/lib.rs
```Rust
//! # My Crate
//!
//! `my_crate` is a collection of utilities to make performing certain
//! calculations more convenient.

/// Adds one to the number given.
// --snip--
///
/// # Examples
///
/// ```
/// let arg = 5;
/// let answer = my_crate::add_one(arg);
///
/// assert_eq!(6, answer);
/// ```
pub fn add_one(x: i32) -> i32 {
    x + 1
}
````
Örnek 14-2: Bir bütün olarak `my_crate` sandığı belgeleri

Dikkat ederseniz `//!` işaretiyle başlayan son satırdan sonra herhangi bir kod bulunmuyor. Bunun nedeni yorum satırını `///` yerine `//!` işaretiyle başlattığımız için satırın bitiminden sonra gelen satırlardaki öğeler yerine işaretin bulunduğu satırdaki öğeyi belgelenecek olmasıdır. O nedenle, bu yorumu içeren öğe, sandık kökü olan src/lib.rs dosyası olacağından bu yorum satırları tüm sandık için yapılan açıklamaları içerir. 

Artık `cargo doc --open` komutu çalıştırıldığında işaretlenmiş bu satırlar aşağıda yer alan Şekil 14-2'de gösterildiği gibi `my_crate` belgesinin ön sayfasında, sandıktaki genel öğeler listesinin üstünde görüntülenir: 

![Şekil 14-2](https://doc.rust-lang.org/book/img/trpl14-02.png)
Şekil 14-2: Yorumlar dahil olmak üzere `my_crate` sandığını bir bütün olarak tanımlayan belgeler

Öğeler içindeki belge yorumları, özellikle sandık ve modülleri tanımlamak için kullanışlıdır. Bu yorumları, paketlerinizi kullanacak olan kişilerin paket düzenini anlamalarına yardımcı olmak ve paket kapsamının genel amacını açıklamak için kullanın.

## [pub use](https://doc.rust-lang.org/book/ch14-02-publishing-to-crates-io.html#exporting-a-convenient-public-api-with-pub-use) Ön Ekiyle Bir API'yi Genel Kullanıma Uyarlamak
Bölüm 7'de, `mod` anahtar kelimesini kullanarak kodumuzu modüller halinde nasıl düzenleyeceğinizi, `pub` anahtar sözcüğüyle öğelerin nasıl genelleştirileceğini ve `use` anahtar kelimesiyle de öğelerin kapsama nasıl dahil edileceğini ele almıştık. Ancak, bir sandık geliştirirken sizin için anlamlı olan yapı, kullanıcılarınız için çok uygun olmayabilir. Yapılarınızı çok katmanlı hiyerarşik bir yapıda düzenlemek isteyebilirsiniz, ancak daha sonra bu hiyerarşinin derinlerinde tanımlamış bulunduğunuz herhangi bir türü kullanmak isteyen kişiler, bu türe erişmekte sorun yaşayabilirler. Ayrıca bu türe `use my_crate::UsefulType;` şeklinde bir söz dizimiyle ulaşmak yerine `my_crate::some_module::another_module::UsefulType;` şeklinde bir söz dizimi kullanmak oldukça rahatsız edici olabilir. 

Bir sandık yayınlarken herkese açık olarak tasarlanmış olan API'nizin yapısı oldukça önemlidir. Sandığınızı kullanan kişiler bu yapıya sizin kadar aşina olmadıklarından, sandığınız büyüyüp karmaşık bir modül hiyerarşisine dönüştüğünde, kullanmak istedikleri API parçalarına ulaşmakta zorluk çekebilirler. İyi haber şu ki, eğer yapınız başkalarının kütüphanelerinde kullanılmaya uygun değilse, API Hiyararşisini ve belki tasarımını baştan başa yeniden düzenlemek yerine, `pub use` anahtar kelimelerini kullanarak, tüm öeğeleriyle bu yapının genel kullanıma uygun bir sürümünü yeniden ihraç edebilirsiniz. Yeniden ihraç herkese açık bir öğeyi bir konumda alır ve başka bir yerde, başka bir konumda tanımlanmış gibi herkese açık hale getirir.

Örneğin, sanatsal kavramları modellemek için `art` adında bir kütüphane tasarladığımızı varsayalım. Örnek 14-3'te de görüleceği gibi; bu kütüphanenin içinde `PrimaryColor` ve `SecondaryColor` adında iki enum içeren bir `kinds` modülü ve `mix` adındaki işlevi içeren `utils` modülü bulunmaktadır:

Dosya: src/lib.rs
```Rust
//! # Art
//!
//! A library for modeling artistic concepts.

pub mod kinds {
    /// The primary colors according to the RYB color model.
    pub enum PrimaryColor {
        Red,
        Yellow,
        Blue,
    }

    /// The secondary colors according to the RYB color model.
    pub enum SecondaryColor {
        Orange,
        Green,
        Purple,
    }
}

pub mod utils {
    use crate::kinds::*;

    /// Combines two primary colors in equal amounts to create
    /// a secondary color.
    pub fn mix(c1: PrimaryColor, c2: PrimaryColor) -> SecondaryColor {
        // --snip--
    }
}
````
Örnek 14-3: `kinds` ve `utils` modüllerinde düzenlenmiş öğeleri içeren bir `art` kütüphanesi
![resim 14-3](https://doc.rust-lang.org/book/img/trpl14-03.png)
Şekil 14-3, `cargo doc` tarafından üretilen bu sandık için dokümantasyonun ön sayfasının nasıl görüneceğini göstermektedir:

Ön sayfada `PrimaryColor` ve `SecondaryColor` türleri ve mix() işlevinin listelenmediğine dikkat edin. Onları görmek için `kind` ve `utils` bağlantılarına tıklamak gereklidir. Bu kütüphaneye bağımlı başka bir sandık, halihazırda tanımlanmış olan `art` modül yapısına ait öğeleri kendi kapsamına alabilmek için `use` ifadesini kullanmak zorunda kalacaktır. Örnek 14-4, `art` sandığındaki `PrimaryColor` ve `mix` öğelerini kullanan başka bir sandık örneğini gösterir:

Dosya: src/main.rs
```Rust
use art::kinds::PrimaryColor;
use art::utils::mix;

fn main() {
    let red = PrimaryColor::Red;
    let yellow = PrimaryColor::Yellow;
    mix(red, yellow);
}
````
Örnek 14-4: İç yapısının dışa aktarılmasıyla öğeleri kullanılabilir hale gelen `art` sandığı 

Örnek 14-4'te yer alan `art` sandığını kullanan kodun programcısı, `PrimaryColor` türünün `kinds` modülünde ve `mix()` işlevinin de  `utils` modülünde olduğunu bulabilmelidir. Sandığın modül yapısı, `art` sandığını geliştiren programcılardan çok, bu sandığı kullanan  programcılar için önemlidir. Sandığın parçalarını `kinds` ve `art` modülleri olarak düzenleyen iç yapı, bu sandığın nasıl kullanılacağını öğrenmek isteyenler için herhangi bir yararlı bilgiler içermediği gibi `art` sandığı modül yapısının yarattığı karmaşa, use ifadelerinde modül adlarını belirtmek isteyen kullanıcıların nereye bakacaklarını bilememelerine neden olacağından kullanışsız bir yapıdır. 

Herkesin kullanımına açık olan API'den iç düzenlemeyi kaldırıp, pub use ifadelerini kullanarak öğeleri en üst düzeyde yeniden dışa aktarabilmek için Örnek 14-3'te yer alan `art` sandığının kodlarını, Örnek 14-5'te görğleceği şekilde yeniden düzenleyebiliriz. 

Dosya: src/lib.rs
```Rust
//! # Art
//!
//! A library for modeling artistic concepts.

pub use self::kinds::PrimaryColor;
pub use self::kinds::SecondaryColor;
pub use self::utils::mix;

pub mod kinds {
    // --snip--
}

pub mod utils {
    // --snip--
}
````
Örnek 14-5: Öğeleri yeniden dışa aktarmak için pub use ifadeleri eklemek

`cargo doc` komutunun bu sandık için oluşturduğu API belgeleri, Şekil 14-4'te görebileceğiniz gibi ön sayfada yeniden dışa aktarımları listeleyerek bağlayacak ve `PrimaryColor` ve `SecondaryColor` türlerini ve `mix` işlevini bulmayı kolaylaştıracaktır.
![Şekil 14-4](https://doc.rust-lang.org/book/img/trpl14-04.png)
Şekil 14-4: Yenilenen dışa aktarımı listeleyen `art` belgelendirmesinin ön sayfası

Artk `art` sandığını kullanacak olan programcılar, ister Örnek 14-5 ve 14-6'da yenilenen daha uygun yapıyı, isterlerse hâlâ kullanılmaya uygun durumda bulunan Örnek 14-4'te ve 14-3'teki iç yapıyı inceleyip kodlarına dahil edebilirler.

Dosya src/main.rs
```Rust
use art::mix;
use art::PrimaryColor;

fn main() {
    // --snip--
}
````
Örnek 16-4: Yeniden dışa aktarılan `art` sandığı öğelerini kullanan bir program

Çok sayıda iç içe modülün olduğu durumlarda, türleri en üst düzeyde yeniden ihraç etmek pub use, sandığı kullanan kişilerin deneyiminde önemli bir fark yaratabilir.  Kullanışlı bir genel amaçlı API oluşturmak bir sanat olduğundan kullanılarınıza en uygun olabilecek tasarım için defalarca tekrar yapmanız gerekebilir. ancak `pub use` kullanımı paket iç yapınızı kullanıcılara sunulduğu halinden ayırabildiği için esneklik sağlar. sandık iç yapılarının genel kullanıma açılmış hallerinden farklı olup olmadığını anlamak için çalışmalarına dahil ettiğiniz başka sandıkları incelemeniz yararlı olacaktır. 

## Bir Crates.io Hesabı Oluşturma
Herhangi bir sandığı yayınlayabilmeniz için öncelikle [crates.io](https://crates.io/) üzerinde bir hesap oluşturmanız ve bir API anahtarı almanız gereklidir. Bunun için [crates.io](https://crates.io/) adresini ziyaret ederek GitHub hesabınız ile giriş yapın. (Her ne kadar gelecekte siteye başka yöntemlerle üye olunacağı planlanmış olsa da şu an için yalnızca GitHub hesaplarımız ile giriş kabul edilmektedir.) Sisteme girişinizi yaptıktan sonra, [https://crates.io/me/](https://crates.io/me/) adresindeki hesap ayarlarınızı ziyaret ederek ve API anahtarınızı alın. Ardından, aşağıdaki gibi API anahtarınızla `cargo login` komutunu çalıştırın:

```bash
$ cargo login abcdefghijklmnopqrstuvwxyz012345
````

Bu komut Cargo'ya API anahtarınızı bildirecek ve onu yerel olarak `~/.cargo/credentials` içinde depolayacaktır. Anahtarınızı size özel olduğunu, gizli kalması ve kimseyle paylaşılmaması gerektiğini unutmayın. Eğer anahtarınızı herhangi bir sebeple birileriyle  paylaşmışsanız, geçerli olanı deerhal iptal ederek yeni bir anahtar oluşturmalısınız.

## Yeni Oluşturulmuş Sandığa Meta Veri Eklemek
Artık hesabınızı oluşturduğunuza göre, yayınlamak istediğiniz bir sandığınızın olduğunu düşünebiliriz. Ancak sandığınızı yayına almadan hemen önce, *Cargo.toml* dosyasının `package` bölümüne sandığınıza ait meta verileri eklemeniz gerekmektedir. 

Yerel olarak bir sandık üzerinde çalışırken, istediğiniz bir sandığı adlandırabilirsiniz. Ancak yayınlama aşamasına geldiğinizde sandığınızın benzersiz bir isme ihtiyacı olacak. [crates.io](https://crates.io/)'daki sandık adları sadece servise ilk alınanın o isme sahip olması üzerinden yürütüldüğünden, bir sandığa ismi bir kez tahsis edildikten sonra o isin başka bir sandığa verilemez. Başka bir ifadeyle bir sandık adı bir kez alındığında başka hiç kimse tarafından aynı isimde bir başka sandık yayınlanamaz. O nedenle sandığınızı [crates.io](https://crates.io/) üzerinde yayınlanmaya kalkışmadan önce site içinde kullanmak istediğiniz isimde başka bir sandığın olup olmadığını araştırmalısınız. Eğer tercih ettiğiniz sandık adı başka bir sandık tarafından kullanılmaktaysa, yeni seçeceğiniz sandık ismini, paketinizin *Cargo.toml* dosyasında bulunan `[package]` bölümüne isim alanı dahil aşağıda gösterildiğine benzer şekliyle düzenlemeniz gerekecektir.

Dosya: Cargo.toml
```Rust
[package]
name = "guessing_game"
````
Her ne kadar benzersiz bir ad seçmiş olsanız bile, sandığı yayınlamak için `cargo publish` komutunu çalıştırdığınızda aşağıdakine benzer bir uyarı ve hata alabilirsiniz:

```bash
$ cargo publish
    Updating crates.io index
warning: manifest has no description, license, license-file, documentation, homepage or repository.
See https://doc.rust-lang.org/cargo/reference/manifest.html#package-metadata for more info.
--snip--
error: api errors (status 200 OK): missing or empty metadata fields: description, license. Please see https://doc.rust-lang.org/cargo/reference/manifest.html for how to upload metadata
````
Bunun sebebi, sandığınızı kullanmak isteyecek programcılar için, sandığınızın neler yaptığını ve hangi koşullar altında kullanılabileceğini düzenleyen açıklama ve lisans bilgileri gibi önemli detayları atlamış olmanızdır. Bu hatayı düzeltebilmeniz için gerekli olan bilgileri paketinizin *Cargo.toml* dosyasına işlemeniz gerekir. 

Girdiğiniz açıklamalar arama sonuçlarında görüntüleneceğinden en azından bir iki cümle açıklamak eklemeniz yerinde olur. Lisans alanı içinse bir `license` tanımlayıcı değeri vermeniz gereklidir. [Linux Vakfı'nın Yazılım Paketi veri değişimi (SPDX)](http://spdx.org/licenses/), bu alan için kullanabileceğiniz tanımlayıcıları listeler. Örneğin, sandığınızı MIT Lisansı ile lisansladığınızı belirtmek için `MIT` tanımlayıcısını ekleyin:

```bash
[package]
name = "guessing_game"
license = "MIT"
````

SPDX'te görünmeyen bir lisans kullanmak istiyorsanız, söz konusu lisansın metnini bir dosyaya yerleştirmeniz, dosyayı projenize eklemeniz ve ardından `license` alanındaki tanımı `license-file` şeklinde dosya adını tanımlayacak şekilde belirtmeniz gerekmektedir.

Projeniz için hangi lisansın daha uygun olacağına dair rehberlik bu kitabın kapsamı dışındadır. Rust topluluğunun pek çok üyesi Rust'ın da yapmayı seçtiği gibi projelerini `MIT VEYA Apache-2.0` olarak çifte lisans kullanarak lisanslar. Bu uygulama biçimi projenizi `OR` ekiyle birden fazla lisansa sahip olacak şekilde lisans tanımlayıcısıyla ilişkilendirebileceğinizi gösterir.

`cargo new` komutunu kullanarak benzersiz bir adla oluşturduğunuz sandığınıza, yazar adı, sürüm bilgisi, paket açıklaması ve lisans tanımlayıcısını eklediğinizde, yayına hazır olan projenizin `Cargo.toml` dosyası aşağıdaki yapıya benzeyecektir:

```bash
[package]
name = "guessing_game"
version = "0.1.0"
authors = ["Your Name <you@example.com>"]
edition = "2018"
description = "A fun game where you guess what number the computer has chosen."
license = "MIT OR Apache-2.0"

[dependencies]
````

Başkalarının sandıklarınızı daha kolay keşfedep kullanabilmesini sağlamak için kullanabileceğiniz daga fazla meta-veriye [Cargo Belgeleri](https://doc.rust-lang.org/cargo/)nden kolaylıkla ulaşabilirisiniz.

## Bir sandığı Crates.io'da Yayınlamak
Artık bir hesap oluşturduğunuza, API anahtarınızı kaydettiğinize, sandığınız için benzersiz bir ad seçtiğinize ve gerekli meta verileri belirttiğinize göre onu yayınlamaya hazırsınız demektir. Bir sandık yayınlamak, başkalarının kullanması için crates.io'ya belirli bir sürümün yüklenmesi anlamına gelmektedir. 

Bir sandık yayınlandıktan sonra, sürümün üzerine tekrar yazılamayacak, kod o yayın için silinip değiştirilemeyecek şekilde kalıcı olacağından oldukça dikkatli olmanız gereklidir. Bunun nedeni [Crates.io](https://crates.io/)'nun ana hedefi olan kalıcı bir kod arşivi oluşturmaktır. Bu hedef ve tutarlılık sayesinde [crates.io](https://crates.io/)'dan gelen sandıklara bağımlı tüm projeler zaman içerisinde sorunsuz bir şekilde derlenerek çalışmaya devam eder. Sürüm silme işlemine izin vermek bu hedefi olanaksız hale getireceğinden buna izin verilmez ancak sandık versiyonu yayınlama konusunda ise bir sınır yoktur. 

Şimdi `cargo publish` komutunu tekrar çalıştırdığınızda paketinizin başarıyla yayınlandığını göreceksiniz:

```bash
$ cargo publish
    Updating crates.io index
   Packaging guessing_game v0.1.0 (file:///projects/guessing_game)
   Verifying guessing_game v0.1.0 (file:///projects/guessing_game)
   Compiling guessing_game v0.1.0
(file:///projects/guessing_game/target/package/guessing_game-0.1.0)
    Finished dev [unoptimized + debuginfo] target(s) in 0.19s
   Uploading guessing_game v0.1.0 (file:///projects/guessing_game)
````

Tebrikler! Artık kodunuzu Rust topluluğuyla paylaştığınıza göre, herkes sandığınızı projesine bağımlılık olarak kolayca ekleyebilir.

## Mevcut Bir Sandığın Yeni Sürümünü Yayınlamak
Önceden yayınlamış olduğunuz bir sandıkta tüm kullanıcıları etkileyen bir değişiklik yaptığınızda veya yeni bir sürüm yayınlamaya hazır olduğunuzda sandığınızın *Cargo.toml* dosyasının `version` bölümünü uygun şekilde değiştirerek paketinizi yeniden yayınlayabilirsiniz. Gerçekleştirdiğiniz değişikliğin türüne göre sonraki sürüm numarasına karar verebilmeniz için [Anlamsal Sürüm Oluşturma Kuralları](http://semver.org/)nı kullanabilirsiniz. Bu işlemlerin ardından `cargo publish` komutunu kullanarak yeni sürümünüzü kolaylıkla yayınlayabilirsiniz.

## Sürümleri [`cargo yank`](https://doc.rust-lang.org/book/ch14-02-publishing-to-crates-io.html#removing-versions-from-cratesio-with-cargo-yank) Komutu Kullanarak Crates.io Üzerinden Kaldırmak
Yayımlanmış bir sandığın önceki sürümlerini kaldıramasanız dahi, yeni projelerin bu sürümleri kullanmasını ve bağımlılık olarak eklemesini engelleyebilirsiniz. Bu davranış sandık sürümünüzün bir nedenle bozulduğunda yararlıdır. Bu gibi durumlarda, Cargo `yank` adlı bir komut kullanarak o versiyonu geri çekmenizi sağlar. 

Bir sürümün geri çekilmesi -yani *yanking edilmesi*- yeni projelerin bu sürüme bağlanmasını önlerken ona bağımlı olarak tanımlanmış mevcut projelerin bu sürümü indirmeye ve bağımlı olarak kalmasına izin verir. Temel olarak `yank` yani geri çekme işlemi *Cargo.lock* dosyasına işlenmiş projelerin artık sürümü kullanmaya devam edeceğini, ama yeni bağımlılık talepleri için geri çekilmiş olan bu sürümün kullanılmasına daha fazla izin verilmeyeceği anlamına gelmektedir.

Sandığın geri çekilecek versiyonunu için sürüm bilgisi ile birlikte aşağıda gösterilene benzer biçimde `cargo yank` kodunu çalıştırmanız gerekir:

```bash
$ cargo yank --vers 1.0.1
````

Geri çekme işlemini `cargo yank` komutuna `--undo` ekleyerek geçersiz hale getirebilir ve projelerin bu sürüme yeniden bağlanmasına izin verebilirsiniz:

```bash
$ cargo yank --vers 1.0.1 --undo
````

Geri çekme işlemi var olan kodları silmez. Örneklersek, projenize yanlışlıkla eklediğiniz bazı özel veya sır olarak kalması gereken kodları `yank` özelliğini kullanarak silmeniz mümkün olmayacağından bu sırları hemen kaldırmanız gerekir. 
