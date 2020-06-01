--------------------------------ASP.NET CORE MVC PROJESİ XUNIT BİRİM VE ENTEGRASYON TESTLERİ-------------------------------

Öncelikle Core projesi içerisindeki blog controller test edildiğinden dolayı test classı BlogController bir bağımlılık olarak IRepositoy alır. Mock işlemini yapacağımız interface IRepositorydir. Burası taklit edilecek alandır. Asıl test edilecek nesne BlogControllerdır. Bu sınıf içerisindeki metotlar test edilir. Ekleme, silme, güncelleme işlemlerinin yapılması için elimizde blog bulunması gerekir.

INDEX METODU TESTİ:

İlk test edilecek metot Index metodudur. Burada iki durum karşımıza çıkar. 

-> İlk durumda Geriye view döndürülmesi beklenir.

-> İkinci durumda ise viewle birlikte bir data (blog) dönmesi beklenir. 

İlk durumda ViewResult durumu test edilir. O yüzden herhangi bir mock işlemi yapılmasına gerek yoktur. Index metodundan gelen verinin viewresult olup olmadığı yazılan kodlarla test edilir. 

İkinci durumda içerisinde model dönüp dönmediğine dair test yapılacaktır. İlk durumda mock işlemi yapılmadığı için gelen Result’ın değeri boş olarak gelir. Daha sonra ise Getall() metodu üzerinde mock işlemi yapılır. Bu yapılan işlemlerde Index metodu içerisinde GetAll metodu çalıştığı zaman geriye belirtmiş olduğumuz blog dönecektir.

*Veri tabanına ulaşmak yerine belirtmiş olduğumuz datalar döndürülecek de diyebiliriz. Mock işleminin amacı çalışmayı taklit etmektedir. Assert metoduyla hem bir testi kontrol etme işlemi, hem de generic olarak ne verildiyse onun da geriye dönmesi sağlanır.

Üç kere kontrol işlemi yapılması sağlandı:
-	İlk olarak viewresult döndüğüne dair bir kontrol,
-	İkinci olarak bu viewresult’un değerinin bir bloglist olup olmadığına dair bir kontrol,
-	Son olarak gelen bloglist sayısının yazılan değer olup olmadığına dair bir kontrol
Bu işlemler başarılı bir şekilde gerçekleşirse test başarılıdır.

DETAILS METODU TESTİ:

Bu metot içerisinde test edilecek üç kısım bulunmaktadır:

->	İd null dönerse geriye NotFound dönmesi durumu,

->	İd gönderildiğinde null olmayıp, GetById ile veri tabanında yoksa ve blog’ a null girilirse geriye NotFound dönmesi durumu,

->	Bu adımların tamamlanması durumunda geriye Model’da blog dönüp dönmediği durumudur.


İlk adımın test aşaması -> yazılan metotta Detail sayfasının id’nin null olma durumunda geriye RedirectToIndexActiona dönmesi gerekir. Ardından contoller sayfasındaki datails metodu çağrılır. İçerine null gönderilir. Gereken işlemler yapıldıktan sonra RedirectToAction’ da döndürme işlemi sağlandı fakat hangi sayfaya döndüğüne dair bir test yapılması gerekmektedir. Index sayfasına döndürülme işlemi ve ActionName’ de kontrol işlemi sağlandı. Parametre almadığından dolayı Fact attiribute’ü  ile birlikte test metodu olarak işaretlendi.

İkinci adımın test aşaması -> Fact attiribute kullanarak Details sayfasında geçersiz bir id olması durumu sonrası geriye NotFound dönmesi gerekmektedir. Mock üzerinden setup oluşturulur. 0 gönderildiğinde geriye null döndürülmesi için gereken kod yazılır. Controller içerisinde GetById metodu çalıştığı zaman mock kütüphanesi içerisinde yazılan sahte GetById metodu çalışır ve geriye null bir blog döndürülür. (id’ nin 0 verilmesi durumunda oluşur.)
Yazılan redirect durumu Assert metodu üzerinden bir int değer test edileceği belirtilir. Durum kodunun 404 olması gerekir. Http protokolünde bazı geri dönüş tipleri vardır. Burada durum kodu da test edilmiş olmaktadır.

Son adımın test aşaması -> İd geçerli olması durumunda blog dönmesi beklenir. İkinci ve üçüncü durumlarda GetById metodu çalışır fakat ilk durumda çalışmadığından dolayı mock işlemi yapılır. GetById metodu çalışırsa ve geriye blog id gelirse geriye idsi 1 olan blog nesnesi döndürülür. Veri tabanına bağlanıp id’si 1 olan blog verisini almak yerine kendi oluşturduğumuz idsi 1 olan blog verisi alınır. Gönderilen blog, bizim göndermiş olduğumuz blog ise bu metot sonucunda gelen result blog idye eşit olup olmadığı kontrol edilir. 

CREATE METODU TESTİ:

Bu metot içerisinde iki ihtimal bulunmaktadır.
İlk olarak ortada olan bir Create butonu ikinci olarak da kullanıcılar blog ekleyeceği zaman çalışacak olan HttpPost attiribute sahip olan Create butonudur.
İlk kısım kolayca test edilebilir fakat ikinci alan için test edilecek bazı durumlar bulunmaktadır:

->	Blog üzerinden baslik gelmez ise Index sayfasına yönlendirilir. 

->	Model başarılı ise gelen blog nesnesi aynı sayfaya yönlendirilir ve o sayfa güncellenir.

Metot içerisinde hata oluşturuldu. Bu hatanın oluşturulma sebebi return(view)’in Index’e yönelmek yerine aynı sayfaya yönelip yönelmediğinin tespini ve ayrıca içerisine blog modeli gönderip göndermediğini kontrol etmektir.
İlgili metodun içerisinde repository olduğunda mock işlemi yapılması gerekir. Bu işlemin yapılıp yapılmamasına şu şekilde karar verilir: 
Test classı içerisinde constructorda bulunan respository kısmına MockBehavior isimli bir metot eklenir. Bu metot Strict ve Looose isimli iki değer alabilir. Eğer Strict alırsa ana proje içerisinde bulunan tüm metotlar mock işleminden geçmek zorundadır. Default olan Loose değerini alırsa, controller içerisindeki metotla işlem yapılmayacaksa mock işlemi yapılmasına gerek yoktur.
Biz işlem yaparken ilgili Model State geçerli olduğunda ilgili alan çalışıyor olsa da sonucu ile ilgilenmediğimizden dolayı Create metodu yerine, Index sayfasıyla ilgili olan kısmı mock işlemine tabi tutulur. Sonucunda dönüş değerine bakılır.
ModelState geçerli olduğu durumda blog işlemi gerçekleşip gerçekleşmediğine dair (yani repository içerisindeki create metodu çalışıyor mu ?) test kontrolü yapılır.
Boş bir blog nesnesi oluşturulur. Create metoduna mock işlemi yapılır. Bizden bir blog girmemiz istenir, “herhangi bir blog” olduğunu belirtmek için It.IsAny<Blog> metodunu kullanırız. Blog içerisine hangi blog nesnesini verdiysek CallBack metodu kullanarak çalıştırıyoruz. Gelen blog nesnesi yeni bir değişkene aktarılır ve controller içerisindeki create metodu çağrılır. Create metodunun bir kez çalışıp çalışmadığı doğrulanır.  Sahte create metoduna herhangi bir metot geldiyse ve bu metot en az bir kere çalıştıysa testimiz başarılı olur. Assert metodu ile, id’si 1 olan blog verisinin yeni eklenen blog verisine eşit olup olmadığının kontrolü sağlanır. İstersek başka özelliklerini de karşılaştırabiliriz.
Yeni testimizde create metodunun hiç çalışmaması durumu ele alınır. Test metodu içerisine hata eklenir. Controller içerisinde create metodunun çalışıp, Repository kısmındaki create metodunun çalışmaması durumunu istenildiği için Times.Never olarak ayarlandı.
 
EDIT METODUNUN TEST EDİLMESİ 

İlk durumda ->  Index sayfasına dönüp dönmediği test edilir. Id’ nin null olma durumu kontrol edilir. Metot parametre almadığından dolayı fact olarak işaretlenir. Assert metodu kullanılarak Index sayfası olup olmadığı kontrol edilir.

İkinci -> durumda blog controller içerisine id olarak olmayan bir id girilmesi durumunda blog = null olmalı ve geriye notfound döndürmelidir. Bu işlem test edilir. 

Üçüncü durumda ->  ilk iki aşamayı geçtiği takdirde blog’ u olan bir id verildiği zaman geriye blog dönüp dönülmeyeceği test edilir. 2 olan nesneyi sorgulamak için İnlineData(2) olarak belirlendi. Başarılı bir şekilde çalıştığında geriye blog dönmesi beklenir. Id’si 2 olan blog alınır. GetById kullanıldığı için mock işlemi yapılır. Setup içerisinde GetById çalışması ve blogid gelmesi durumu belirtilir ve blog nesnesini döndürmesi beklenir. Controller içerisindeki Edit metoduna var olan blogid verilir. ViewResult tipinde olup olmadığının kontrolü sağlanır. Datayı alma işlemi tapılması gerekir. Assert metodunun yanında IsAssignable metodu daha tanımlanır. Bu işlem sonrasında resultblog üzerinden değerler kontrol edilir. Blog nesnesinde gelen id değerinin, resultblog’dan gelen id ile aynı olması beklenir.Bu metot referans verilip verilemediğini test eder bunun yanı sıra IsType metodu veri tipini kontrol eder. IsAssignable metodu bize ayrıyeten bir interface implement etmiş olan bir classı aynı interface’i implement etmiş olan başka bir classa referans verilmesini sağlar. Miras alınabilme durumunun test edilmesine imkân verir. İçerisinde vermiş olduğumuz nesne eğer object’den miras alıyorsa (yani atanabiliyorsa) bu metot bunu karşılamaktadır fakat object string ile cast edildiği taktirde IsAssignable metodu içerisi false dönecektir çünkü birebir tipi ile karşılaştırır. IsAssignable birbirlerine atanıp atamadığını karşılaştırır. Object olarak string verildiğinde, string classı object classından miras aldığından dolayı atanabilme işlemi gerçekleştirilir.
 
Dördüncü durumda -> id’si 1 olan güncellenip id’si 2 olan veri gönderildiğinde id’ler farklı olacağından dolayı farklı id’ler olduğundan güncellemede hata oluşur ve geriye NotFound hatası döndürülür. Assert.IsType metodu ile kontrol edilir.

Beşinci durumda -> ModelState hatalı ise (baslik alanı boş gönderilirse) geriye Edit sayfasına dönülmelidir. Bunun yanı sıra edit sayfasına gönderilen blog nesnesi, View ile birlikte cshtml’e gönderilir. Bu durum test edilir.

Altıncı durumda ->  ModelState’ in geçerli olduğu durumda Index sayfasına dönüp dönülmediği test edilir. Update alanı test edilmediğinden, o kısımla ilgili mock işlemine gerek yoktur. 

Yedinci durumda -> Edit içerisinde ModelStateValid test edilir ve Update metodu çalıştırılır. Blog Controller içerisinde Update metodu void olduğundan mock işlemi yaparken de geriye değer dönmez. Blog üzerinden First metodu kullanılarak data alınır ve metotdan gelen id’ye eşitlenir. Updata metodu içerisinde blog nesnesi tanımlanır. Controller üzerinden edit metodu çalıştırılır içerisine blogid, blog değerleri overload edilir. Bu işlemlerin ardından doğrulama işlemi yapılır. Edit metodu çalıştığı zaman Update metodunun çalışıp çalışmadığı kontrol edilir.

DELETE METODUNUN TEST EDİLMESİ

İlk adımda ->  id’nin alınma durumu test edilir. Id’nin null olması durumunda geriye NotFound döndürülür. Redirect diyerek gelen datanın NotFoundResult olma durumu kontrol edilir.

İkinci adımda -> id’ye sahip blog olup olmadığının kontrolü sağlanıp eğer yoksa geriye NotFound döndürülmesi sağlanır. Öncelikle null blog nesnesi oluşturulur. Mock üzerinden setup içerisine x üzerinden GetById verilip bu metoda blogid geldiğinde geriye nullolan blog nesnesi döndürülür. Controller üzerinden delete metodu çalıştırılır.

Üçüncü adımda -> Blog nesnesinde var olan bir id gönderilir ve geriye blog modeli döndürülüyor mu kontrol edilir. id’si 1 olan ürün almaya çalışılır. x.id metottan gelen blogid’ye eşitlenir. Repo’nun GetById methodu çalışacak ve blogıd gelirse blogıd dönmesi beklenir. Controller üzerinden Delete methodunu çalıştırılır. Assert üzerinden delete’ten gelen resultun viewresult olmadığı test edilir. Daha sonra model test edilir.

Dördüncü adımda -> Index sayfasına dönüp dönmediği  testi gerçekleştirilir. 

Beşinci adımda -> Blog controller üzerinde delete methodunun çalışıp çalışmadığı test edilir. Action çalıştığı zaman delete methodunun çalıştığı kontrol edilir. Daha sonra x.id metottan gelen blogid’ye eşitlenir.  Repo üzerinden delete methodu çalışması beklenir. Burada delete methodu geriye bir şey döndürmez. Sonrasında doğrulama işlemi en az  1 kere yapılır.

------------------------------------API PROJECT TEST XUNIT BİRİM VE ENTEGRASYON TESTLERİ---------------------------

BLOGAPICONTROLLER OLUŞTURMA

Var olan proje içerisinde Api oluşturmak gereksiz kod tekrarlarını önlemeyi sağlar. İçerideki kodlara GetBlog methodu verilirse methodların çalışması sağlanır. OkResult hepsini IActionResult ile implement ettikten sonra istenilen durum kodları döndürülür. Id , güncellenecek Id den farklı olduğu zaman Clint hatası olur. O yüzden BadRequest döndürülür.

POSTMAN PROGRAMI ARACILIĞI İLE API’LERİ TEST ETME

Postman ücretsiz API aracıdır.  Bu uygulamada API’ler test edilir. Postman’da  istekleri ve isteklerin method tipi belirlenir ve sonuç görüntülenir. Controller üzerinden yapılan isteğin tipine göre methodlara erişilir. Controller üzerinden endpointler çağırılır.

GETBLOG() METHODUNUN TEST EDİLMESİ

API içindeki methodlar test edilir. OkResult Notfound gibi değerler http durum kodlarıyla beraber içerisinde  datalar döner.

GETBLOG(İNT) METHODUNUN TEST EDİLMESİ

Birinci adımda GetBlog methodunun int değer alan overloadının test işlemi gerçekleşir. 2 seneryo ele alınır. 

Birinci senaryoda Blog null olduğu  zaman NotFound döner mi diye test yapılır. X.GetById 0 olduğu zaman null döndüğü test edilir.

İkinci adımda ise GetBlog methodunun int değer alan overloadının test işlemine devam edilir. 

İkinci senaryoda geriye OkResult dönüyor mu testi yapılır. X.GetById 1 olduğu zaman blog döndüğü test edilir.

PUTBLOG METHODUNUN TEST EDİLMESİ

Güncelleme işlemlerinin gerçekleştiği alandır. Bir blog nesnesi üzerinden bu methodla beraber güncelleme yapılır.

Birinci adımda BadRequest() methodunun obje almayan durumu sorgulanır. x.Id  blog’a eşit ise karşılaştığı ilk data’yı alır.

İkinci adımda PutBlog uygun bir şekilde çalıştığı zaman tek bir methodta  update methodunun çalıştığı hem de geriye NoContent dönüp dönmediği test edilir.

POSTBLOG METHODUNUN TEST EDİLMESİ

Create methodu test edilir  ve geriye CreatedAtAction methodu geriye dönüyor mu, tek bir senaryoda test edilir. GetBlog çalışıyor mu diye test edilir.

DELETEBLOG METHODUNUN TEST EDİLMESİ

ActionResult üzerinden değil ActionResult sınıfı üzerinden almış olduğu Generic bloğu üzerinden test gerçekleşir. İstenilen durum kodunda geriye dönülebilir.

Birinci adım da Bloğun null olma durumu ve NotFound dönme olayı test edilir. Controller üzerinden delete methodu çalışır.

İkici adım da geçerli bir BlogId verildiği zaman delete methodunun çalışıp çalışmadığı ve NoContentin dönüp dönmediği test edilir.

