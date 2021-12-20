# Zaman Damgası Javascript/Typescript İle Kullanımı


Zaman Damgası Tübitak'ın tabiri ile;

> Zaman Damgaları belli bir verinin belirtilen bir tarihte var olduğunu kanıtlarlar. Zaman Damgası Sunucusu, zaman damgalarını imzalamak için açık anahtar teknolojisini kullanarak, verinin bütünlüğünü ve belirli bir tarihteki varlığını onaylar. 
> Bir sözleşmenin imzalandığı, paranın transfer edildiği, başvurunun yapıldığı vs. tarih ve saati kanıtlama ihtiyacı günümüz e-ticaret, e-devlet uygulamaları için hayati önem taşımaktadır. Bununla birlikte yeni bir çizim, tasarım, fotoğraf, düşünce, araştırma, formül, algoritma, kitap gibi fikri ve mülki kullanım hakkı elde edilmek istenen her türlü elektronik veri için zaman damgası alınabilir.
> 5070 sayılı Elektronik İmza Kanununa göre Zaman Damgası: Bir elektronik verinin, üretildiği, değiştirildiği, gönderildiği, alındığı ve / veya kaydedildiği zamanın tespit edilmesi amacıyla, elektronik sertifika hizmet sağlayıcısı tarafından elektronik imzayla doğrulanan kaydı, ifade eder.
> [Kaynak](https://kamusm.bilgem.tubitak.gov.tr/urunler/zaman_damgasi/)

Şeklinde açıklanmaktadır. 
Peki zaman damgasını biz nasıl kullanabiliriz?

Zaman damgasını kullanabilmek için bir sertifika sağlayıcısından kontör almanız gerekmektedir. Kontörünüzü aldığınızda size iletilen bilgiler ile hemen zaman damgasını kullanmaya başlayabilirsiniz. 

### Test Amaçlı kullanım

Eğer zaman damgasını sadece test etmek istiyorsanız [bu](https://kamusm.bilgem.tubitak.gov.tr/urunler/zaman_damgasi/ucretsiz_zaman_damgasi_istemci_yazilimi.jsp) adresten adımları takip edip Tübitak'tan gerekli bilgileri talep edebilirsiniz. 

### Zamane

Zamane programı Tübitak'ın sitesinden indirebileceğiniz ve istediğiniz dosyayı kendi bilgileriniz ile zaman damgası ile damgalayabileceğiniz bir uygulamadır. Bu yazıdaki amacımız bu programın yaptığı işlemin aynısını programımızın yapabilmesidir. 

Zamane programını [buradan](https://kamusm.bilgem.tubitak.gov.tr/urunler/zaman_damgasi/ucretsiz_zaman_damgasi_istemci_yazilimi.jsp) indirip deneyebilirsiniz.

### Zamane KS
Tübitak programlarımızda kullanabilmemiz için ZamaneKS adında bi program vermektedir. Bu programı Zamane uygulaması ile aynı sayfadan indirilebilir. 

![ZamaneKS](https://imagedelivery.net/zuNY_yYAKP0SbzyN2Kq1PQ/65e8a796-90ac-4069-3b53-cf0b8477b800/blog)

# ZamaneJS

ZamaneJS Appac Yazılım tarafından hazırlanan ZamaneKS programının javascript veya typescript ile kullanımını kolaylaştırmak üzere geliştirilmiştir.  

### Kurulum

ZamaneJS bir npm paketidir ve projenize `npm i --save zamanejs` veya  `yarn add zamanejs` komutu ile ekleyebilirsiniz. Kütüphanede Typescript tanımlamaları mevcuttur ve Typescript ile kolayca kullanabilirsiniz. 

Zaman damgası bilgilerinizi aldığınızda size bir sunucu adresi, bağlantı portu, kullanıcı numarası ve parola vermektedirler. Bu bilgiler ile zamane
```typescript
const zamene = new Zamane({
  tssAddress: 'http://tzd.kamusm.gov.tr', // Sunucu Adresi
  tssPort: '80', // Bağlantı portu
  customerNo: '00000', // Kullanıcı Numarası
  customerPassword: 'a1b2c3d4', // Parola
});
```

bu kod ile Zamane objemizi başlatıyoruz. Başlatıığımız anda eğer `Java must be installed on $PATH` hatası alırsanız bilgisayarınızda java yüklü değil demektir. 

### Java? 🤮
Malesef zamane uygulaması bir çok platformda çalışması gerektiği için java dilinde yazılmış. 

Kütüphanede ilk defa bir fonksyon çağırdığınızda tübitak sunucularından zameneks programı indirilip bilgisayarınızda `/tmp/zamanejs/zamane.jar`  konumuna indirilir. 


## Kredi?

Evet yapılan her işlem için hesabınızdan bir kredi azalır. Uygulama içinde kod ile kredi sorgulaması yapabilirsiniz. Maalesef bildiğim kadarıyla kredi almanın otomatize bir yolu bulunmamaktadır. 
```typescript
zamene.checkCredit().then(credit => {
  console.log("Available Credit", credit);
});
```

## Bir Dosyayı Damgalama

Artık bir dosyayı zaman damgası ile damgalayabiliriz. 

Örneğin elimizde `example.txt` adında bir dosya olsun. Bu dosyanın şu anki tarihte var olduğunu kanıtlamak istiyoruz. 
```typescript
zamene.stampFile("example.txt").then(stampLocation => { 
  // aynı dizinde example.txt.zd adında bir dosya oluşacaktır
  console.log("Timestamp File Location: ", stampLocation);
});
```
Üstteki kodu çalıştırdığımızda`example.txt` dosyasının yanında `example.txt.zd` adında bir dosya görüyoruz. bu dosya `example.txt` dosyasının damgalandığı tarihte var olduğunu kanıtlayan bir dosyadır. Artık oluşan dosyayı **orjinal dosya ile beraber** arşivleyebilirsiniz. Eğer orjinal dosyayı kaybederseniz zaman damgası bir işe yaramaz bunun yüzünden orijinal dosyayı beraber saklamayı unutmayınız!

## Bir Yazıyı Damgalama

Üstteki örnekte eğer dosyayı okuyabiliryor ama bulunduğu dizine yazamıyorsanız hata verecektir. Sunucularda genellikle böyle güvenlik önlemleri bulunmaktadır. Bunun için elinizdeki metni geçici olarak bir dosyaya koyup o dosya ile işlem yapabilirsiniz veya ZamaneJS kütüphanesi size bunu otomatik olarak yapabilir. 
```typescript
zamene.stampContents("Test Contents").then(stamp => {
  console.log("Stamp Result", stamp);
});
```
Üstteki örnek `Test Contents` metnini zaman damgası yaparak `stamp` değişkeninde size zaman damgası verisini `Buffer` objesi olarak döndürür. 

> Uyarı
> Eğer dönen veriyi düzgün saklayamazsanız zaman damgası doğrulanamaz. Veriyi saklarken aşağıdaki gibi veriyi base64 formatında saklamanız önerilir.
>```typescript
>stamp.toString('base64')
>```

### Şifreler Nerede Saklanmalıdır?

Zamane Şifrenizi lütfen kodunuzun içinde saklamayınız! Şifreler gibi gizli bilgilerin saklanması için en iyi yer Enviroment değişkenleridir. Zamane objenizi oluşturarak Enviroment'tan bilgileri alabilirsiniz.

```typescript
const  zamene = new Zamane({
  tssAddress:  process.env.ZAMANE_ADDRESS!,
  tssPort:  process.env.ZAMANE_PORT!,
  customerNo:  process.env.ZAMANE_CUSTOMER_NO!,
  customerPassword:  process.env.ZAMAME_CUSTOMER_PASSWORD!
});
```
bunu yaptıktan sonra projenize kolaylık olsun diye `dotenv` kütüphanesini kurabilirsiniz.
`yarn add dotenv` veya `npm i --save dotenv` ile kurulum yaptıktan sonra projenize `.env` adında bir dosya oluşturup gizli kalması gereken değerleri bu dosyaya aşağıdaki gibi koyabilirsiniz. 
```bash
ZAMANE_ADDRESS=http://zd.kamusm.gov.tr
ZAMANE_PORT=80
ZAMANE_CUSTOMER_NO=00000
ZAMAME_CUSTOMER_PASSWORD="a1b2c3d4"
```

Sonra projenizin kendi importlarınızdan önce çalışacak şekilde aşağıdaki programı eklemeniz gerekmektedir. Zamane objesini oluşturmadan önce çağırılmalıdır. 

```typescript
require("dotenv").config();
```

### Uyarı!

Projenizde `.env` dosyasını asla **git'e eklememelisiniz**!
Eklenmediğinden emin olmak için projenizin `.gitignore` dosyasında en altına `.env` satırını ekleyebilirsiniz. Eğer bu dosya bulunmuyorsa kendiniz oluşturup ekleyebilirsiniz. Eğer yanlışlıkla git'e gönderdiyseniz [bu](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository) makele size yardımcı olacaktır. Zamane bilgilerinizin başka birinin eline geçmesi ile kötü amaçlı biri bütün kredilerinizi hızlıca kullanıp size parasal zarar getirebilir. Lütfen Enviroment dosyalarınız saklayın ve kimse ile paylaşmayın.


## Son

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/deed.tr"><img alt="Creative Commons Lisansı" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />Bu eser <a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.tr"> Creative Commons Atıf-GayriTicari-AynıLisanslaPaylaş 4.0 Uluslararası Lisansı</a> ile lisanslanmıştır.
