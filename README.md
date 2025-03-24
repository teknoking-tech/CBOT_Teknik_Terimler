# Veritabanı ve Web Geliştirme Kapsamlı Çalışma Rehberi

## 1. ACID Özellikleri

ACID, veritabanı işlemlerinin güvenilir bir şekilde işlenmesini sağlayan Atomiklik (Atomicity), Tutarlılık (Consistency), Yalıtım (Isolation) ve Dayanıklılık (Durability) özelliklerinin kısaltmasıdır.

### Atomiklik (Atomicity)
- **Tanım**: Bir işlem, ya tamamen gerçekleşen ya da hiç gerçekleşmeyen tek, bölünemez bir birim olarak ele alınır.
- **Örnek**: Bir banka transferinde, hem para çekme hem de para yatırma işlemi ya birlikte başarılı olur ya da hiçbiri gerçekleşmez.
- **Uygulama**: İşlem günlükleri, geri alma mekanizmaları.

### Tutarlılık (Consistency)
- **Tanım**: Bir işlem, veritabanını bir geçerli durumdan başka bir geçerli duruma getirir, önceden tanımlanmış tüm kuralları korur.
- **Örnek**: Bir tabloda hesap bakiyesinin negatif olamayacağına dair bir kısıtlama varsa, negatif bakiyeyle sonuçlanan herhangi bir işlem reddedilir.
- **Uygulama**: Kısıtlamalar, tetikleyiciler, doğrulama kuralları.

### Yalıtım (Isolation)
- **Tanım**: Eşzamanlı işlemler, tamamlanana kadar birbirinden izole edilir.
- **Örnek**: Aynı kaydı güncelleyen iki kullanıcı, birbirlerinin değişikliklerine müdahale etmez.
- **Uygulama**: Kilitleme mekanizmaları, izolasyon seviyeleri (Read Uncommitted, Read Committed, Repeatable Read, Serializable).

### Dayanıklılık (Durability)
- **Tanım**: Bir işlem onaylandıktan sonra, sistem arızası durumunda bile kalıcı olarak saklanır.
- **Örnek**: Bir işlem onaylandıktan hemen sonra elektrik kesintisi olursa, değişiklikler yine de korunur.
- **Uygulama**: Önce yazma günlükleme (write-ahead logging), veritabanı yedeklemeleri.

## 2. Durum Yönetimi (State Management)

Durum yönetimi, uygulama durumunun birden çok bileşen veya oturum arasında korunması ve senkronize edilmesi sürecidir.

### İstemci Taraflı Durum Yönetimi
- **Çerezler (Cookies)**: İstemcinin tarayıcısında saklanan küçük veri parçaları.
- **Yerel Depolama (Local Storage)**: Tarayıcı kapatıldıktan sonra bile kalıcı olan tarayıcı depolaması.
- **Oturum Depolama (Session Storage)**: Yalnızca tarayıcı oturumu sırasında var olan tarayıcı depolaması.
- **URL Parametreleri**: URL'de bulunan durum bilgileri.

### Sunucu Taraflı Durum Yönetimi
- **Oturumlar (Sessions)**: Belirli bir kullanıcı oturumuna bağlı sunucuda saklanan bilgiler.
- **Veritabanı Depolama**: Durum bilgilerinin kalıcı depolanması.
- **Önbelleğe Alma (Caching)**: Performansı artırmak için geçici depolama.

## 3. Çerezler (Cookies)

Çerezler, web siteleri tarafından kullanıcının tarayıcısında saklanan küçük veri parçalarıdır.

### Çerez Türleri
- **Oturum Çerezleri**: Geçici, tarayıcı kapatıldığında silinen çerezler.
- **Kalıcı Çerezler**: Süresi dolana kadar veya manuel olarak silinene kadar kalan çerezler.
- **Birinci Taraf Çerezleri**: Ziyaret edilen web sitesi tarafından ayarlanan çerezler.
- **Üçüncü Taraf Çerezleri**: Ziyaret edilen web sitesi dışındaki alan adları tarafından ayarlanan çerezler.

### Çerez Özellikleri
- **Ad ve Değer**: Çerezde saklanan veri.
- **Son Kullanma Tarihi**: Çerezin ne zaman silineceği.
- **Alan Adı (Domain)**: Hangi web sitelerinin çereze erişebileceği.
- **Yol (Path)**: Web sitesi içindeki hangi yolların çereze erişebileceği.
- **Güvenli Bayrağı (Secure Flag)**: Çerezin yalnızca HTTPS üzerinden gönderilmesini sağlar.
- **HttpOnly Bayrağı**: JavaScript'in çereze erişimini engeller.
- **SameSite**: Çerezlerin siteler arası isteklerle ne zaman gönderileceğini kontrol eder.

### Uygulama
```javascript
// Çerez ayarlama
document.cookie = "username=john; expires=Thu, 18 Dec 2025 12:00:00 UTC; path=/; secure; httpOnly";

// Çerezleri okuma
const cookies = document.cookie.split(';');
```

## 4. Oturumlar (Sessions)

Oturumlar, aynı kullanıcıdan gelen birden çok sayfa isteği arasında durum bilgisini koruyan sunucu taraflı depolama mekanizmalarıdır.

### Oturum İş Akışı
1. Kullanıcı bir web sitesini ilk kez ziyaret eder.
2. Sunucu yeni bir oturum oluşturur ve benzersiz bir oturum kimliği üretir.
3. Oturum kimliği istemciye gönderilir (genellikle çerez olarak).
4. Sonraki istekler için istemci oturum kimliğini gönderir.
5. Sunucu, oturum verilerini almak için oturum kimliğini kullanır.

### Oturum Depolama Yöntemleri
- **Bellekte Saklama**: Hızlı ancak sunucu yeniden başlatılırsa kaybedilir.
- **Veritabanı**: Kalıcı ancak daha yavaş.
- **Dağıtılmış Önbellek**: Hız ve güvenilirlik dengesi (örn. Redis, Memcached).

### Oturum Güvenlik Konuları
- Oturum çalma önleme
- Oturum sabitleme koruması
- Oturum zaman aşımı politikaları
- Siteler arası istek sahteciliği (CSRF) koruması

## 5. JSON Web Token'ları (JWT)

JWT'ler, bilgilerin taraflar arasında JSON nesnesi olarak güvenli bir şekilde iletilmesi için kompakt, kendi kendine yeten bir yöntemdir.

### JWT Yapısı
- **Başlık (Header)**: Token türünü ve imzalama algoritmasını içerir.
- **Payload**: İddiaları (bir varlık hakkındaki ifadeler) içerir.
- **İmza (Signature)**: Token'ın değiştirilmediğinden emin olur.

### JWT İş Akışı
1. Kullanıcı kimlik bilgileriyle doğrulama yapar.
2. Sunucu kimlik bilgilerini doğrular ve bir JWT oluşturur.
3. Sunucu JWT'yi istemciye gönderir.
4. İstemci JWT'yi saklar (localStorage, sessionStorage, çerez).
5. İstemci, sonraki isteklerde JWT'yi ekler.
6. Sunucu JWT imzasını doğrular ve isteği işler.

### JWT Avantajları
- Durumsuz kimlik doğrulama
- Kullanıcı verilerini içerebilir (veritabanı sorgularını azaltır)
- Farklı alan adları arasında kullanılabilir

### JWT Güvenlik Konuları
- Token süresi
- Uygun depolama (hassas uygulamalar için HttpOnly çerezler)
- İmza doğrulama
- XSS ve CSRF saldırılarına karşı koruma

## 6. Nesne Yönelimli Programlama İlkeleri

Nesne yönelimli programlama (OOP), veri ve bu veri üzerinde çalışan kodları içeren "nesneler" kavramına dayalı bir programlama paradigmasıdır.

### Kapsülleme (Encapsulation)
- **Tanım**: Veriyi ve bu veri üzerinde çalışan metotları tek bir birim (sınıf) içinde birleştirme.
- **Faydalar**: Bilgi gizleme, veriye kontrollü erişim.
- **Uygulama**: Özel (private) alanlar, getter/setter metotları.

### Kalıtım (Inheritance)
- **Tanım**: Yeni bir sınıfın mevcut bir sınıftan özellikler ve davranışlar devralmasını sağlayan bir mekanizma.
- **Faydalar**: Kod yeniden kullanımı, hiyerarşik ilişkiler kurma.
- **Türler**: Tekli, çoklu (tüm dillerde desteklenmez), çok seviyeli, hiyerarşik.

### Çok Biçimlilik (Polymorphism)
- **Tanım**: Farklı sınıfların ortak bir arayüz aracılığıyla aynı sınıfın örnekleri olarak ele alınabilme yeteneği.
- **Türler**: 
  - Metot geçersiz kılma (runtime polymorphism)
  - Metot aşırı yükleme (compile-time polymorphism)
- **Faydalar**: Esneklik, genişletilebilirlik.

### Soyutlama (Abstraction)
- **Tanım**: Karmaşık uygulama detaylarını gizleyerek yalnızca gerekli özellikleri gösterme.
- **Uygulama**: Soyut sınıflar, arayüzler.
- **Faydalar**: Karmaşık sistemleri basitleştirir, bir nesnenin nasıl yaptığından ziyade ne yaptığına odaklanır.

## 7. MVC Mimarisi

Model-View-Controller (MVC), bir uygulamayı üç ana bileşene ayıran bir mimari desenidir.

### Model
- **Sorumluluk**: Uygulamanın verilerini, mantığını ve kurallarını yönetir.
- **Özellikler**: Kullanıcı arayüzünden bağımsızdır, veri ve iş kurallarını doğrudan yönetir.
- **Örnek**: Veritabanı erişim katmanı, iş mantığı, veri doğrulama.

### Görünüm (View)
- **Sorumluluk**: Görsel öğeleri ve kullanıcı arayüzü bileşenlerini temsil eder.
- **Özellikler**: Görüntülemek için denetleyiciden bilgi alır, kullanıcı eylemlerini denetleyiciye gönderir.
- **Örnek**: HTML şablonları, JSP sayfaları, React bileşenleri.

### Denetleyici (Controller)
- **Sorumluluk**: Kullanıcı girdilerini işler ve modeli ve görünümü buna göre günceller.
- **Özellikler**: Model ve görünüm arasında aracı görevi görür, tüm iş mantığını işler.
- **Örnek**: İstek işleyicileri, web framework'lerindeki eylem metotları.

### MVC İş Akışı
1. Kullanıcı Görünüm ile etkileşime girer.
2. Denetleyici, Görünümden gelen girdiyi alır.
3. Denetleyici, kullanıcı eylemine göre Modeli günceller.
4. Model verileri işler ve sonuçları Denetleyiciye döndürür.
5. Denetleyici uygun Görünümü seçer.
6. Görünüm, Modelden gelen verileri render eder.

### MVC Faydaları
- İlgilerin ayrılması
- Daha kolay bakım ve test
- Paralel geliştirme
- Aynı model için birden çok görünüm

## 8. REST API

Representational State Transfer (REST), ağ uygulamalarını tasarlamak için bir mimari stildir.

### REST İlkeleri
- **Durumsuzluk (Statelessness)**: Sunucu, istekler arasında istemci bağlamını saklamaz.
- **İstemci-Sunucu Mimarisi**: İstemci ve sunucu arasında ilgilerin ayrılması.
- **Önbelleğe Alınabilirlik**: Yanıtlar, önbelleğe alınabilir veya alınamaz olarak kendilerini tanımlamalıdır.
- **Katmanlı Sistem**: İstemci, doğrudan son sunucuya bağlı olup olmadığını anlayamaz.
- **Tekdüze Arayüz**: Kaynaklarla etkileşim için standartlaştırılmış bir yol.

### REST Kaynakları
- **Tanımlama**: Kaynaklar URI'ler ile tanımlanır.
- **Manipülasyon**: Kaynaklar, temsiller aracılığıyla manipüle edilir.
- **Kendi Kendini Açıklayan Mesajlar**: Her mesaj, nasıl işleneceğini açıklamak için yeterli bilgi içerir.
- **Hipermedia**: İstemciler, yalnızca sunucular tarafından dinamik olarak sağlanan hipermedia bağlantıları aracılığıyla durum geçişleri yapar.

### REST'te HTTP Metotları
- **GET**: Kaynak(ları) getir.
- **POST**: Yeni bir kaynak oluştur.
- **PUT**: Bir kaynağı güncelle (tamamen değiştirme).
- **PATCH**: Bir kaynağı kısmen güncelle.
- **DELETE**: Bir kaynağı kaldır.

### REST'te Durum Kodları
- **2xx**: Başarı (200 OK, 201 Created, 204 No Content).
- **3xx**: Yönlendirme (301 Moved Permanently, 304 Not Modified).
- **4xx**: İstemci Hatası (400 Bad Request, 401 Unauthorized, 404 Not Found).
- **5xx**: Sunucu Hatası (500 Internal Server Error, 503 Service Unavailable).

### REST API Tasarım En İyi Uygulamaları
- Endpoint yollarında fiil değil, isim kullanma
- Koleksiyonlar için çoğul isimler kullanma
- İlişkileri temsil etmek için iç içe kaynaklar kullanma
- Uygun HTTP durum kodlarını kullanma
- HATEOAS (Hypermedia as the Engine of Application State) uygulanması

## 9. SOAP API

Simple Object Access Protocol (SOAP), web servislerinde yapılandırılmış bilgi alışverişi için bir protokoldür.

### SOAP Özellikleri
- **XML tabanlı**: Mesaj formatı için XML kullanır.
- **Protokolden bağımsız**: HTTP, SMTP, TCP vb. ile çalışabilir.
- **Standartlaştırılmış**: Katı W3C standartlarına uyar.
- **Yerleşik hata işleme**: Önceden tanımlanmış hata işleme mekanizmalarına sahiptir.

### SOAP Mesaj Yapısı
- **Envelope**: SOAP mesajı olarak XML belgesini tanımlayan kök öğe.
- **Header**: Uygulama özellikli bilgiler içeren isteğe bağlı öğe.
- **Body**: Asıl mesajı içerir.
- **Fault**: Hata mesajları için isteğe bağlı öğe.

### SOAP ve REST Karşılaştırması
- **Mesaj Formatı**: SOAP XML kullanır, REST genellikle JSON kullanır.
- **Bant Genişliği**: SOAP, XML'nin ayrıntılı olması nedeniyle daha fazla bant genişliği gerektirir.
- **Önbelleğe Alma**: REST önbelleğe almayı destekler, SOAP'ın sınırlı önbelleğe alma özelliği vardır.
- **Güvenlik**: SOAP yerleşik güvenliğe sahiptir (WS-Security), REST taşıma düzeyi güvenliğine (HTTPS) dayanır.
- **Durum**: SOAP durumlu veya durumsuz olabilir, REST durumsuz olmalıdır.

### SOAP Kullanım Durumları
- Yüksek güvenlik gerektiren kurumsal düzey uygulamalar
- Garantili güvenilirlik gerektiren uygulamalar
- Resmi sözleşmeler (WSDL) gerektiren uygulamalar
- Durumlu süreçler gerektiren işlemler

## 10. Hız Sınırlama (Rate Limiting)

Hız sınırlama, bir istemcinin belirli bir süre içinde bir API'ye yapabileceği istek sayısını kısıtlar.

### Hız Sınırlama Stratejileri
- **Sabit Pencere**: Sabit zaman penceresi başına istekleri sayma.
- **Kayan Pencere**: Zaman pencereleri arasında daha kademeli geçiş.
- **Token Bucket**: Sabit bir hızda token'lar eklenir, her istek bir token tüketir.
- **Leaky Bucket**: İstekleri sabit bir hızda işler, fazla istekleri sıraya koyar.

### Hız Sınırlama Uygulaması
- **Başlıklar**: 
  - `X-RateLimit-Limit`: Bir dönemde izin verilen maksimum istek sayısı.
  - `X-RateLimit-Remaining`: Mevcut dönemde kalan istek sayısı.
  - `X-RateLimit-Reset`: Limitin sıfırlandığı zaman.
- **Durum Kodu**: Limit aşıldığında 429 Too Many Requests.

### Hız Sınırlama Faydaları
- Kötüye kullanımı ve DoS saldırılarını önler
- Kaynakların adil kullanımını sağlar
- Backend hizmetlerini aşırı yükten korur
- API maliyetlerini yönetir

### Hız Sınırlama Konuları
- Sınırlama sırasında kullanıcı deneyimi
- Farklı kullanıcı seviyeleri için farklı limitler
- Dağıtık sistemlerin ele alınması
- Uygun istemci bildirimi

## 11. Web Servisleri ve Veritabanı Bağlantıları İçin Docker Container

Docker, uygulamaları konteynerlerde geliştirmek, dağıtmak ve çalıştırmak için bir platformdur.

### Docker Bileşenleri
- **Dockerfile**: Docker imajı oluşturmak için talimatlar içeren metin belgesi.
- **Image**: Konteynerler oluşturmak için kullanılan salt okunur şablon.
- **Container**: Bir imajın çalıştırılabilir örneği.
- **Registry**: Docker imajları için depo (örn. Docker Hub).
- **Docker Compose**: Çoklu konteyner uygulamalarını tanımlamak ve çalıştırmak için araç.

### Web Servisleri İçin Docker
- **Faydalar**: Tutarlı ortamlar, izolasyon, ölçeklenebilirlik.
- **Yaygın Kullanım**: Her servisin kendi konteynerinde çalıştığı mikro servis mimarisi.
- **Uygulama**: Web servisini bir Dockerfile'da tanımlama, gerekli portları açma.

### Veritabanı Bağlantıları İçin Docker
- **Konular**: Veri kalıcılığı, ağ yapılandırması, güvenlik.
- **Volumes**: Veritabanı verilerini konteyner dışında kalıcı tutmak için kullanılır.
- **Örnek**: Veritabanı konteynerine bağlanan web uygulaması konteyneri.

### Web ve Veritabanı Kurulumu İçin Docker Compose Örneği
```yaml
version: '3'
services:
  web:
    build: ./web
    ports:
      - "8080:8080"
    depends_on:
      - db
    environment:
      DB_HOST: db
      DB_PORT: 1521
      DB_USER: app
      DB_PASSWORD: password
  
  db:
    image: oracle/database:19.3.0
    volumes:
      - oracle-data:/opt/oracle/oradata
    environment:
      ORACLE_SID: ORCLCDB
      ORACLE_PDB: ORCLPDB1
      ORACLE_PWD: password

volumes:
  oracle-data:
```

## 12. Oracle DB ve İlişkisel Veritabanı İlişkileri

Oracle Veritabanı, Oracle Corporation tarafından üretilen çok modelli bir veritabanı yönetim sistemidir.

### Oracle Veritabanı Özellikleri
- **Veri Tipleri**: VARCHAR2, NUMBER, DATE, TIMESTAMP, CLOB, BLOB, vb.
- **Tablespace'ler**: Veritabanı nesneleri için mantıksal depolama birimleri.
- **PL/SQL**: Oracle'ın SQL'in prosedürel uzantısı.
- **Bölümleme (Partitioning)**: Büyük tabloların daha küçük, yönetilebilir parçalara bölünmesi.
- **RAC (Real Application Clusters)**: Tek bir veritabanına erişen birden çok örnek.

### İlişkisel Veritabanı İlişkileri
- **Bire Bir (1:1)**: A tablosundaki bir kayıt, B tablosundaki tam olarak bir kayıtla ilişkilidir.
  - **Örnek**: Kişi ve Pasaport.
  - **Uygulama**: Bir tablodaki birincil anahtar, diğer tabloda yabancı anahtardır.

- **Bire Çok (1:N)**: A tablosundaki bir kayıt, B tablosundaki birden çok kayıtla ilişkilidir.
  - **Örnek**: Müşteri ve Siparişler.
  - **Uygulama**: "Bir" tablonun birincil anahtarı, "çok" tabloda bir yabancı anahtar haline gelir.

- **Çoka Çok (M:N)**: A tablosundaki birden çok kayıt, B tablosundaki birden çok kayıtla ilişkilidir.
  - **Örnek**: Öğrenciler ve Dersler.
  - **Uygulama**: Her iki tabloya da yabancı anahtarlar içeren bağlantı/köprü tablosu.

- **Kendi Kendine Referans**: Bir tablodaki kayıtlar, aynı tablodaki diğer kayıtlarla ilişkilidir.
  - **Örnek**: Çalışanlar ve Yöneticiler (ikisi de Çalışan tablosunda).
  - **Uygulama**: Yabancı anahtar, aynı tablonun birincil anahtarına referans verir.

### Oracle SQL Birleştirmeleri (Joins)
- **INNER JOIN**: Her iki tabloda da eşleşme olduğunda satırları döndürür.
- **LEFT JOIN**: Sol tablodaki tüm satırları ve sağ tablodaki eşleşen satırları döndürür.
- **RIGHT JOIN**: Sağ tablodaki tüm satırları ve sol tablodaki eşleşen satırları döndürür.
- **FULL JOIN**: İki tablodan herhangi birinde eşleşme olduğunda satırları döndürür.
- **CROSS JOIN**: İki tablonun Kartezyen çarpımını döndürür.

### Oracle DB'de Normalizasyon
- **Birinci Normal Form (1NF)**: Yinelenen sütunları ortadan kaldırma, ilişkili veriler için ayrı tablolar oluşturma.
- **İkinci Normal Form (2NF)**: 1NF gereksinimlerini karşılama, veri alt kümelerini ayrı tablolara taşıma.
- **Üçüncü Normal Form (3NF)**: 2NF gereksinimlerini karşılama, birincil anahtara bağlı olmayan sütunları kaldırma.

## 13. JWT Token Hız Sınırlama

JWT token hız sınırlama, API erişimini kontrol etmek için JSON Web Token'ları hız sınırlama stratejileriyle birleştirir.

### Uygulama Yaklaşımları
- **Token Tabanlı Hız Sınırlama**: JWT payload'ında hız sınırlama bilgilerini içerme.
- **Kullanıcı Tabanlı Hız Sınırlama**: Kullanımı izlemek için JWT'den kullanıcı tanımlayıcısını kullanma.
- **Rol Tabanlı Hız Sınırlama**: JWT'de kodlanmış kullanıcı rollerine göre farklı limitler uygulama.

### JWT Token Hız Sınırlama İş Akışı
1. Kullanıcı kimlik doğrulaması yapar ve bir JWT alır.
2. JWT, hız sınırlama bilgileri dahil olmak üzere kullanıcı hakkında iddialar içerir.
3. JWT ile her API isteği için sunucu:
   - JWT'yi doğrular.
   - Kullanıcı/rol bilgilerini çıkarır.
   - Mevcut kullanımı limitlere göre kontrol eder.
   - Ya isteği işler ya da 429 durum kodu döndürür.

### JWT Hız Sınırlama Güvenlik Konuları
- Token değiştirmeyi önleme (uygun imza doğrulaması kullanma)
- Uygun son kullanma süreleri belirleme
- Dağıtılmış sistemler için merkezi bir hız sınırlama hizmeti düşünme
- Yanıt başlıklarında hız sınırlama bilgilerini dahil etme

## 14. Langchain SQL Agent ve Kullanımı

Langchain, dil modelleri tarafından desteklenen uygulamalar geliştirmek için bir çerçevedir ve SQL Agent, veritabanı etkileşimleri için özelleştirilmiş bir bileşendir.

### Langchain SQL Agent Bileşenleri
- **Dil Modelleri**: Ajanı destekleyen AI modelleri (örn. GPT modelleri).
- **SQL Veritabanı Bağlantısı**: Çeşitli SQL veritabanlarına bağlanmak için arayüz.
- **Araçlar**: Sorgu oluşturma gibi belirli görevler için özelleştirilmiş bileşenler.
- **Bellek**: Bağlamı ve konuşma geçmişini korumak için sistemler.

### Langchain SQL Agent Yetenekleri
- **Doğal Dilden SQL'e**: Doğal dil sorularını SQL sorgularına çevirme.
- **Şema Anlama**: Veritabanı şemasını otomatik olarak analiz etme ve anlama.
- **Sorgu Optimizasyonu**: Oluşturulan sorgular için iyileştirmeler önerme.
- **Sonuç Açıklama**: Sorgu sonuçlarını doğal dilde açıklama.

### Uygulama Örneği
```python
from langchain.agents import create_sql_agent
from langchain.agents.agent_toolkits import SQLDatabaseToolkit
from langchain.sql_database import SQLDatabase
from langchain.llms import OpenAI

# Veritabanına bağlanma
db = SQLDatabase.from_uri("postgresql://username:password@host:port/database")

# Toolkit ve ajan oluşturma
toolkit = SQLDatabaseToolkit(db=db)
agent = create_sql_agent(
    llm=OpenAI(temperature=0),
    toolkit=toolkit,
    verbose=True
)

# Ajana sorgu yapma
agent.run("Geçen ay 5'ten fazla ürün satın alan kaç müşteri var?")
```

### Langchain SQL Agent Kullanım Durumları
- **Veri Analizi**: Teknik olmayan kullanıcıların veritabanlarını sorgulamasına izin verme.
- **Veritabanı Keşfi**: Yeni veritabanı şemalarını hızlıca anlama.
- **Rapor Oluşturma**: Doğal dil kullanarak veritabanı verilerinden raporlar oluşturma.
- **Veri Doğrulama**: Veri bütünlüğünü ve iş kurallarını kontrol etme.

# Olası Sınav Soruları ve Cevapları

## ACID Özellikleri

**S1: Bir işlem ACID'deki Tutarlılık özelliğini ihlal ederse ne olur?**
C: Bir işlem Tutarlılık özelliğini ihlal ederse, veritabanı yönetim sistemi tüm işlemi geri alarak veritabanını önceki geçerli durumuna döndürür. İşlem onaylanmaz ve yapılan herhangi bir değişiklik geri alınır.

**S2: Bankacılık sisteminde, bir transfer işlemi para A Hesabından B Hesabına aktarır. Hangi ACID özelliği, hem borç hem de alacak işlemlerinin ya ikisinin de başarılı olmasını ya da hiçbirinin olmamasını sağlar?**
C: Atomiklik, her iki işlemin (A Hesabından borç ve B Hesabına alacak) tek, bölünemez bir birim olarak ele alınmasını sağlar. Ya her iki işlem de başarılı olur, ya da herhangi biri başarısız olursa, tüm işlem geri alınır.

**S3: Yalıtım özelliği "kirli okuma" problemini nasıl önler?**
C: Yalıtım, bir işlem tarafından yapılan değişikliklerin, ilk işlem onaylanana kadar diğer işlemler tarafından görünmemesini sağlayarak kirli okumaları önler. Bu genellikle çeşitli izolasyon seviyeleri aracılığıyla uygulanır, daha yüksek seviyeler kirli okumalara karşı daha güçlü garantiler sağlar.

## Durum Yönetimi

**S1: localStorage ve sessionStorage'ı kalıcılık ve kapsam açısından karşılaştırın.**
C: 
- localStorage: Veriler tarayıcı kapatıldıktan sonra bile kalıcıdır ve tarayıcı oturumları arasında kullanılabilir. Sona erme süresi yoktur ve aynı alan adındaki herhangi bir sayfadan erişilebilir.
- sessionStorage: Veriler yalnızca sayfa oturumu süresince kalıcıdır. Tarayıcı sekmesi kapatıldığında veriler temizlenir. Belirli tarayıcı sekmesi/penceresi ile sınırlıdır.

**S2: Modern web uygulamalarında durum yönetimi için çerezleri kullanmanın temel dezavantajları nelerdir?**
C: Dezavantajlar şunlardır:
- Sınırlı depolama kapasitesi (genellikle çerez başına 4KB)
- Her HTTP isteğiyle gönderilir, bant genişliği kullanımını artırır
- Düzgün güvenlik sağlanmazsa CSRF saldırılarına karşı savunmasızdır
- Gizlilik endişeleri ve uyumluluk gereksinimleri (GDPR, CCPA)
- Kullanıcılar tarafından devre dışı bırakılabilir
- Serilleştirme olmadan karmaşık veri yapılarını saklamak için kullanılamaz

## Çerezler

**S1: Çerezlerde "SameSite" özelliğini ayarlamanın amacını ve etkisini açıklayın.**
C: SameSite özelliği, CSRF saldırılarını önlemeye yardımcı olmak için çerezlerin siteler arası isteklerle ne zaman gönderileceğini belirler. Üç olası değeri vardır:
- Strict: Çerezler yalnızca birinci taraf bağlamında (aynı site) gönderilir
- Lax: Çerezler, harici bağlantılardan siteye gidildiğinde gönderilir
- None: Çerezler, siteler arası istekler dahil tüm bağlamlarda gönderilir (Secure bayrağı gerektirir)
Bu özelliği düzgün ayarlamak, çerezlerin farklı siteler arasında nasıl paylaşıldığını kontrol ederek güvenliği artırır.

**S2: JavaScript kullanarak HTTP-only bir çerez nasıl oluşturulur ve neden bunu yapmak istersiniz?**
C: HTTP-only çerezler doğrudan JavaScript ile oluşturulamaz. Bunlar, HTTP yanıt başlıkları kullanılarak sunucu tarafından ayarlanmalıdır:

```
Set-Cookie: name=value; HttpOnly
```

Kimlik doğrulama token'ları gibi hassas veriler için HTTP-only çerezleri kullanmak istersiniz çünkü bunlara JavaScript ile erişilemez, bu da çerez verilerini çalmaya çalışan siteler arası komut dosyası (XSS) saldırılarına karşı koruma sağlar.

## Oturumlar

**S1: Oturum kimliklerini çerezlerde veya URL parametrelerinde saklamanın güvenlik etkileri nelerdir?**
C: 
- Çerezler: HttpOnly ve Secure bayrakları ayarlanabildiği için daha güvenlidir, JavaScript erişimini önler ve yalnızca HTTPS üzerinden iletilmesini sağlar. Ancak, düzgün korunmazlarsa CSRF saldırılarına karşı savunmasızdır.
- URL parametreleri: Son derece güvensizdir çünkü oturum kimlikleri:
  - Kullanıcının ekranına bakan herkes tarafından kolayca görülebilir
  - Tarayıcı geçmişi, sunucu günlükleri ve proxy günlüklerinde saklanabilir
  - URL'leri kopyalayıp yapıştırırken yanlışlıkla paylaşılabilir
  - Harici sitelere bağlanırken Referer başlıkları aracılığıyla sızdırılabilir
  - Kullanıcılar oturum parametresi olmadan sayfaları yer işaretlediğinde kaybolabilir

**S2: Oturum sabitleme saldırılarının nasıl çalıştığını ve bunların nasıl önlenebileceğini açıklayın.**
C: Bir oturum sabitleme saldırısında, saldırgan geçerli bir oturum başlatır ve kurbanı bu oturum tanımlayıcısını kullanmaya yönlendirir. Kurban doğrulama yaptığında, saldırgan yetkisiz erişim kazanmak için aynı oturumu kullanabilir.

Önleme yöntemleri şunları içerir:
- Giriş/kimlik doğrulama sonrası oturum kimliklerini yenileme
- IP kontrolü ile oturum kaynağını doğrulama
- Uygun çerez özelliklerini ayarlama (HttpOnly, Secure, SameSite)
- Daha kısa oturum zaman aşımları uygulama
- Hassas işlemler için ek kimlik doğrulama faktörleri kullanma

## JSON Web Token'ları (JWT)

**S1: Hassas bilgiler neden JWT payload'ında saklanmamalıdır?**
C: Hassas bilgiler JWT payload'ında saklanmamalıdır çünkü:
- JWT'ler varsayılan olarak şifrelenmez, yalnızca imzalanır
- Payload, token'a sahip olan herkes tarafından kolayca çözülebilir
- Şifrelenmiş olsa bile, güvensiz saklanırsa token ele geçirilebilir
- Token'lar, HTTPS kullanılmıyorsa iletim sırasında yakalanabilir
- Payload'daki hassas veriler, token hırsızlığının etkisini artırır

Bunun yerine, hassas bilgiler sunucuda saklanmalı ve JWT yalnızca bu bilgileri aramak için gereken tanımlayıcıları içermelidir.

**S2: JWT'leri localStorage'da saklama ile HttpOnly çerezlerde saklama arasındaki farkları açıklayın.**
C:
- localStorage:
  - JavaScript tarafından erişilebilir, XSS saldırılarına karşı savunmasızdır
  - İsteklerle otomatik olarak gönderilmez (manuel olarak dahil edilmelidir)
  - CSRF saldırılarına maruz kalmaz
  - Çerezlere kıyasla boyut sınırlamaları yoktur
  - Uygun uygulamayla alt alan adları arasında kullanılabilir

- HttpOnly çerezler:
  - JavaScript tarafından erişilemez, XSS saldırılarına karşı koruma sağlar
  - Alan adına yapılan her istekle otomatik olarak gönderilir
  - SameSite özelliğiyle korunmadıkça CSRF saldırılarına karşı savunmasızdır
  - Boyut sınırlamalarına tabidir (genellikle 4KB)
  - Secure bayrağı ve son kullanma süresi gibi ek korumalar uygulayabilir

Yüksek düzeyde hassas uygulamalar için, CSRF koruması ihtiyacına rağmen genellikle HttpOnly çerezler tercih edilir.

## Nesne Yönelimli Programlama İlkeleri

**S1: Kapsülleme ilkesi kod bakımını nasıl iyileştirir?**
C: Kapsülleme, kod bakımını şu şekilde iyileştirir:
- Uygulama detaylarını gizleyerek, dış kodu etkilemeden dahili değişiklikler yapılmasına olanak tanır
- Nesne durumuna doğrudan erişimi kısıtlayarak geçersiz durumları önler
- Nesne ile etkileşim için net bir arayüz sağlar
- İlgili işlevselliği ve verileri tek bir birim içinde yerelleştirir
- Bağımlılıkları azaltarak kodu anlamayı kolaylaştırır
- Doğrulama mantığının setter metotlarında merkezileştirilmesine olanak tanır

**S2: Çoklu kalıtımda "elmas problemi" nedir ve Java ve C# gibi diller bunu nasıl ele alır?**
C: Elmas problemi, bir sınıfın ortak bir taban sınıftan miras alan iki sınıftan miras aldığında çoklu kalıtımda ortaya çıkar. Bu, ortak taban sınıf metotlarının hangi uygulamasının kullanılacağı konusunda belirsizlik yaratır.

Java bunu şu şekilde ele alır:
- Sınıfların çoklu kalıtımına izin vermez
- Arayüzlerin çoklu kalıtımını destekler
- Açık çözüm kurallarıyla arayüzlerde varsayılan metotlar sunar

C# bunu şu şekilde ele alır:
- Sınıfların çoklu kalıtımına izin vermez
- Arayüzlerin çoklu kalıtımını destekler
- Belirsizlik olduğunda açık arayüz uygulaması gerektirir

## MVC Mimarisi

**S1: MVC mimarisinde, Görünümün (View) doğrudan Modeli değiştirmesi neden kötü bir uygulama olarak kabul edilir?**
C: Görünümün doğrudan Modeli değiştirmesi kötü bir uygulamadır çünkü:
- İlgilerin ayrılması ilkesini bozar
- Sunum katmanı ile veri katmanı arasında sıkı bağlantı oluşturur
- Uygulamayı test etmeyi zorlaştırır
- Modeldeki değişikliklerin birden çok Görünümde değişiklik gerektirmesi durumunda bakımı karmaşıklaştırır
- Aynı Modelin farklı Görünümlerle yeniden kullanılmasını zorlaştırır
- Uygulama mantığını ve kullanıcı girdisini işlemesi gereken Denetleyiciyi atlar

**S2: MVC deseni, MVVM (Model-View-ViewModel) deseninden nasıl farklılaşır?**
C: MVC ve MVVM arasındaki temel farklar:

- MVC:
  - Denetleyici kullanıcı girdisini işler ve Modeli günceller
  - Görünüm nispeten pasiftir, Modelden gelen verileri render eder
  - Denetleyici hem Görünümün hem de Modelin farkındadır
  - Denetleyiciler ve Görünümler arasında bire çok ilişki vardır

- MVVM:
  - ViewModel, Model verilerini Görünüm için dönüştüren bir veri dönüştürücüsü görevi görür
  - Görünüm ve ViewModel arasında otomatik senkronizasyon için veri bağlama kullanır
  - Görünüm, MVC'ye kıyasla daha aktiftir, daha fazla sunum mantığına sahiptir
  - ViewModel'in Görünüme doğrudan referansı yoktur
  - Özellikle veri bağlama yeteneklerine sahip olay odaklı programlama framework'leri için tasarlanmıştır

MVVM genellikle veri bağlama yeteneklerine sahip modern UI framework'leri için tercih edilirken, MVC daha genel amaçlıdır.

## REST API

**S1: İdempotency REST API'ler için neden önemlidir ve hangi HTTP metotları idempotenttir?**
C: İdempotency, aynı isteği birden çok kez yapmanın, bir kez yapmayla aynı etkiye sahip olmasını sağlar. Bu, özellikle ağ sorunlarının istemcilerin istekleri yeniden denemesine neden olabileceği dağıtılmış sistemlerde güvenilirlik için önemlidir.

İdempotent HTTP metotları:
- GET: Bir kaynağı birden çok kez almak, durumunu değiştirmez
- PUT: Aynı veriyle bir kaynağı birden çok kez değiştirmek, aynı durumla sonuçlanır
- DELETE: Zaten kaldırılmış bir kaynağı kaldırmanın ek etkisi yoktur
- HEAD: Gövde döndürmeden sadece başlıkları döndürür, GET'e benzer
- OPTIONS: Hedef kaynak için iletişim seçeneklerini döndürür

İdempotent olmayan metotlar:
- POST: Aynı kaynağı birden çok kez oluşturmak genellikle birden çok kaynak oluşturur
- PATCH: Uygulama ve payload yapısına bağlı olabilir

**S2: REST API'lerde HATEOAS kavramını açıklayın ve HATEOAS uyumlu bir yanıt örneği verin.**
C: HATEOAS (Hypermedia as the Engine of Application State), istemcilerin uygulamayla tamamen sunucular tarafından dinamik olarak sağlanan hipermedia aracılığıyla etkileşime girdiği REST mimarisinin bir kısıtlamasıdır. İstemcilerin, hipermedia hakkında genel bir anlayışın ötesinde API ile nasıl etkileşime gireceği hakkında önceden bilgiye ihtiyacı yoktur.

HATEOAS uyumlu yanıt örneği:
```json
{
  "id": 123,
  "name": "Örnek Ürün",
  "price": 19.99,
  "_links": {
    "self": {
      "href": "https://api.example.com/products/123"
    },
    "reviews": {
      "href": "https://api.example.com/products/123/reviews"
    },
    "related": {
      "href": "https://api.example.com/products/123/related"
    },
    "update": {
      "href": "https://api.example.com/products/123",
      "method": "PUT"
    },
    "delete": {
      "href": "https://api.example.com/products/123",
      "method": "DELETE"
    }
  }
}
```

## SOAP API

**S1: WSDL belgesinin temel bileşenleri nelerdir ve SOAP servislerindeki amacı nedir?**
C: WSDL (Web Services Description Language), SOAP servisleri için XML tabanlı bir arayüz tanımlama dilidir. Temel bileşenleri şunlardır:

- **Types**: Hizmette kullanılan veri tiplerini tanımlar
- **Message**: Her işlem için veri öğelerini tanımlar
- **Port Type**: Gerçekleştirilebilecek işlemleri ve ilgili mesajları tanımlar
- **Binding**: Her port türü için protokolü ve veri formatını tanımlar
- **Service**: Hizmetin konumunu (endpoints) tanımlar

WSDL'nin amacı, hizmetin nasıl çağrılabileceği, hangi parametreleri beklediği ve hangi veri yapılarını döndürdüğü hakkında makine tarafından okunabilir bir açıklama sağlamaktır. Hizmet sağlayıcı ve tüketici arasında bir sözleşme olarak hizmet eder ve istemci uygulamaları için otomatik kod oluşturmayı sağlar.

**S2: SOAP'taki WS-Security ile REST'teki OAuth/JWT güvenlik mekanizmalarını karşılaştırın.**
C:

WS-Security (SOAP):
- XML tabanlı güvenlik çerçevesi
- Yalnızca taşıma değil, mesaj düzeyinde güvenlik sağlar
- Şifreleme, dijital imzalar ve güvenlik token'larını destekler
- Bir mesajın belirli bölümlerini güvenli hale getirebilir
- Uygulamalar arasında standartlaştırılmıştır
- Uygulaması daha karmaşıktır
- Daha yüksek işlem yükü
- Kurumsal düzeyde güvenlik gereksinimleri için daha iyidir

OAuth/JWT (REST):
- Token tabanlı güvenlik çerçevesi
- Öncelikle mesaj bütünlüğü değil, yetkilendirme sağlar
- Uygulaması ve kullanması daha basittir
- Daha düşük işlem yükü
- Mobil ve web uygulamaları için daha uygundur
- Daha çok taşıma güvenliğine (HTTPS) dayanır
- Modern dağıtılmış sistemler için daha esnek
- Etki alanları arası kimlik doğrulama senaryoları için daha iyidir

## Hız Sınırlama

**S1: Kayan pencere hız sınırlayıcıyı nasıl uygularsınız ve sabit pencere yaklaşımına göre ne gibi avantajları vardır?**
C: Kayan pencere hız sınırlama uygulaması:

```pseudocode
function checkRateLimit(userId, limit, windowSeconds):
    currentTime = getCurrentTimestamp()
    windowStart = currentTime - windowSeconds
    
    // Pencereden daha eski istekleri sil
    deleteRequestsOlderThan(userId, windowStart)
    
    // Mevcut penceredeki istekleri say
    requestCount = countRequestsInWindow(userId, windowStart, currentTime)
    
    if requestCount >= limit:
        return false // Hız sınırı aşıldı
    
    // Yeni isteği kaydet
    recordRequest(userId, currentTime)
    return true // İsteğe izin verildi
```

Sabit pencereye göre avantajları:
- Pencere sınırlarında istek patlamasını önler
- Zaman içinde daha tutarlı bir istek hızı sağlar
- Hizmet bozulması olasılığını azaltır
- Son istek desenlerinin daha doğru bir temsilini sağlar
- Zaman pencereleri arasında daha yumuşak geçiş sağlar
- Birçok istemcinin pencere sıfırlamasını beklediği "stampeding herd" problemini önler

**S2: Dağıtılmış bir mikro servis mimarisi için bir hız sınırlama sistemi tasarlayın. Ne gibi zorluklarla karşılaşabilirsiniz?**
C: Tasarım bileşenleri:
- Merkezi hız sınırlama hizmeti (örn. Redis tabanlı)
- Servisler arası tutarlı hız sınırlama politikaları
- Dağıtılmış sayaç mekanizması
- İstemci tanımlama sistemi (API anahtarları, kullanıcı kimlikleri, IP adresleri)
- Limit bilgileri için yanıt başlıkları
- Bozulma için devre kesici deseni

Zorluklar:
- Düğümler arasında saat senkronizasyonu
- Dağıtılmış sayaçlarda yarış koşulları
- Gerçek zamanlı limit uygulamasını etkileyen ağ gecikmesi
- Hız sınırlama hizmeti için yük devretme ve yedeklilik
- Servisler arası tutarlı kullanıcı tanımlama
- Dağıtılmış sistemlerde önbellek tutarlılığı
- Hız sınırlama hizmetinin ölçeklenebilirliği
- Kullanıcı seviyesine göre değişen hız sınırlama politikalarını ele alma

## Web Servisleri ve Veritabanı Bağlantıları İçin Docker Container

**S1: Bir web uygulamasını ve veritabanını konteynerleme için güvenlik en iyi uygulamaları nelerdir?**
C: Güvenlik en iyi uygulamaları:
- Resmi temel imajları kullanma ve güncel tutma
- En düşük ayrıcalık ilkesini uygulama (kullanım dışı kök kullanıcılar)
- İmajları güvenlik açıkları için tarama
- Dockerfile'larda veya imajlarda asla kimlik bilgilerini kodlamama
- Hassas veriler için gizli yönetim araçları kullanma
- Uygun ağ segmentasyonu uygulama
- Konteynerlere kaynak sınırlamaları uygulama
- Mümkün olduğunda salt okunur dosya sistemleri kullanma
- Docker daemon'ı güvence altına alma
- Uygun günlükleme ve izleme uygulama
- Saldırı yüzeyini azaltmak için çok aşamalı derlemeler kullanma
- Uygun kullanıcı izinlerini ayarlama
- Sağlık kontrolleri uygulama
- İmaj imzalama için Docker Content Trust kullanma
- Bir konteyner güvenlik platformu kullanmayı düşünme

**S2: Bir web uygulamasının birden çok örneğine sahip konteynerlı bir ortamda veritabanı bağlantı havuzunu nasıl ele alırdınız?**
C: Konteynerlı bir ortamda bağlantı havuzunu ele almak için:

1. Her uygulama konteyneri içinde bir bağlantı havuzu kütüphanesi/framework'ü kullanın:
   - Java için HikariCP
   - PostgreSQL için pgBouncer
   - Apache için DBCP
   - ProxySQL gibi bağlantı havuzu ara yazılımı

2. Havuz parametrelerini uygun şekilde yapılandırın:
   - Örnek sayısına bölünmüş veritabanı limitlerine dayalı maksimum bağlantılar
   - Bağlantı zaman aşımı ayarları
   - Boşta bağlantı yönetimi
   - Bağlantı doğrulama sorguları

3. Veritabanı bağlantısını doğrulamak için sağlık kontrolleri uygulayın

4. Havuz kullanımı için izleme ve metrikler ayarlayın

5. Gelişmiş bağlantı yönetimi için sidecar konteyner deseni kullanmayı düşünün

6. Üstel geri çekilme ile yeniden deneme mekanizmaları uygulayın

7. Ölçeklendirmeyi yönetmek için orkestrasyon araçları (Kubernetes, Docker Swarm) kullanın

8. Konteynerler sonlandırıldığında bağlantıların düzgün temizlenmesini sağlayın

## Oracle DB ve İlişkisel Veritabanı İlişkileri

**S1: Oracle DB'de bağlantı tablosu ile çoka çok ilişkisini uygulama sürecini açıklayın. Tablo oluşturma ve sorgulama için SQL içerin.**
C: Uygulama süreci:

1. Ana tabloları oluşturma:
```sql
CREATE TABLE students (
    student_id NUMBER PRIMARY KEY,
    student_name VARCHAR2(100) NOT NULL
);

CREATE TABLE courses (
    course_id NUMBER PRIMARY KEY,
    course_name VARCHAR2(100) NOT NULL
);
```

2. Yabancı anahtarlarla bağlantı tablosu oluşturma:
```sql
CREATE TABLE student_courses (
    student_id NUMBER,
    course_id NUMBER,
    enrollment_date DATE DEFAULT SYSDATE,
    grade VARCHAR2(2),
    CONSTRAINT pk_student_courses PRIMARY KEY (student_id, course_id),
    CONSTRAINT fk_student FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
    CONSTRAINT fk_course FOREIGN KEY (course_id) REFERENCES courses(course_id) ON DELETE CASCADE
);
```

3. Bir öğrencinin tüm derslerini bulmak için sorgu:
```sql
SELECT c.course_id, c.course_name, sc.enrollment_date, sc.grade
FROM courses c
JOIN student_courses sc ON c.course_id = sc.course_id
WHERE sc.student_id = 123;
```

4. Bir dersteki tüm öğrencileri bulmak için sorgu:
```sql
SELECT s.student_id, s.student_name, sc.enrollment_date, sc.grade
FROM students s
JOIN student_courses sc ON s.student_id = sc.student_id
WHERE sc.course_id = 456;
```

5. Birden çok belirli derse kayıtlı öğrencileri bulmak için sorgu:
```sql
SELECT s.student_id, s.student_name
FROM students s
WHERE s.student_id IN (
    SELECT sc1.student_id 
    FROM student_courses sc1
    WHERE sc1.course_id = 101
    INTERSECT
    SELECT sc2.student_id
    FROM student_courses sc2
    WHERE sc2.course_id = 102
);
```

**S2: Veritabanı normalizasyonu kavramını açıklayın ve normalize edilmemiş bir tabloyu 3NF'ye dönüştürme örneği verin.**
C: Veritabanı normalizasyonu, veri yedekliliğini azaltmak ve veri bütünlüğünü iyileştirmek için bir veritabanını yapılandırma sürecidir.

Örnek:

Normalize edilmemiş tablo:
```
OrderDetails:
| OrderID | CustomerName | CustomerAddress | CustomerCity | CustomerPhone | ProductID | ProductName | ProductPrice | Quantity | OrderDate |
|---------|--------------|-----------------|--------------|---------------|-----------|-------------|--------------|----------|-----------|
| 1001    | John Smith   | 123 Main St     | New York     | 555-1234      | P101      | Laptop      | 1200.00      | 1        | 2023-01-15|
| 1001    | John Smith   | 123 Main St     | New York     | 555-1234      | P102      | Mouse       | 25.00        | 2        | 2023-01-15|
| 1002    | Jane Doe     | 456 Oak Ave     | Chicago      | 555-5678      | P101      | Laptop      | 1200.00      | 1        | 2023-01-16|
```

Birinci Normal Form (1NF) - Tüm öznitelikler atomik olduğu için zaten sağlanmış.

İkinci Normal Form (2NF) - Kısmi bağımlılıkları kaldırma:
```
Customers:
| CustomerID | CustomerName | CustomerAddress | CustomerCity | CustomerPhone |
|------------|--------------|-----------------|--------------|---------------|
| C001       | John Smith   | 123 Main St     | New York     | 555-1234      |
| C002       | Jane Doe     | 456 Oak Ave     | Chicago      | 555-5678      |

Products:
| ProductID | ProductName | ProductPrice |
|-----------|-------------|--------------|
| P101      | Laptop      | 1200.00      |
| P102      | Mouse       | 25.00        |

Orders:
| OrderID | CustomerID | OrderDate   |
|---------|------------|-------------|
| 1001    | C001       | 2023-01-15  |
| 1002    | C002       | 2023-01-16  |

OrderItems:
| OrderID | ProductID | Quantity |
|---------|-----------|----------|
| 1001    | P101      | 1        |
| 1001    | P102      | 2        |
| 1002    | P101      | 1        |
```

Üçüncü Normal Form (3NF) - Geçişli bağımlılıkları kaldırma:
```
Customers:
| CustomerID | CustomerName | CustomerPhone |
|------------|--------------|---------------|
| C001       | John Smith   | 555-1234      |
| C002       | Jane Doe     | 555-5678      |

Addresses:
| AddressID | CustomerID | Address    | City     |
|-----------|------------|------------|----------|
| A001      | C001       | 123 Main St| New York |
| A002      | C002       | 456 Oak Ave| Chicago  |

Products:
| ProductID | ProductName | ProductPrice |
|-----------|-------------|--------------|
| P101      | Laptop      | 1200.00      |
| P102      | Mouse       | 25.00        |

Orders:
| OrderID | CustomerID | OrderDate   |
|---------|------------|-------------|
| 1001    | C001       | 2023-01-15  |
| 1002    | C002       | 2023-01-16  |

OrderItems:
| OrderID | ProductID | Quantity |
|---------|-----------|----------|
| 1001    | P101      | 1        |
| 1001    | P102      | 2        |
| 1002    | P101      | 1        |
```

## JWT Token Hız Sınırlama

**S1: JWT'leri kullanarak ücretsiz kullanıcılar ile premium kullanıcılar için farklı hız limitleri uygulayan bir sistem tasarlayın.**
C: Sistem tasarımı:

1. Kimlik doğrulama hizmeti, kullanıcı seviyesi bilgisi içeren JWT'ler oluşturur:
```json
{
  "sub": "user123",
  "tier": "premium",
  "rate_limits": {
    "api_calls_per_minute": 60,
    "api_calls_per_day": 10000,
    "concurrent_requests": 10
  },
  "exp": 1609459200,
  "iat": 1609372800
}
```

2. Hız sınırlama ara yazılımı:
```pseudocode
function rateLimit(request):
    // JWT'yi çıkar ve doğrula
    jwt = extractJWTFromRequest(request)
    if (!verifyJWT(jwt)):
        return unauthorized()
    
    // Kullanıcı bilgilerini ve hız limitlerini al
    userId = jwt.sub
    tier = jwt.tier
    limits = jwt.rate_limits
    
    // Redis sayaçlarına dayalı hız limitlerini kontrol et
    redisKey = "ratelimit:" + userId + ":minute"
    currentCount = redisIncr(redisKey)
    if (currentCount == 1):
        redisExpire(redisKey, 60) // 60 saniyelik süre ayarla
    
    if (currentCount > limits.api_calls_per_minute):
        return tooManyRequests()
    
    // Günlük limiti kontrol et
    dailyKey = "ratelimit:" + userId + ":day"
    dailyCount = redisIncr(dailyKey)
    if (dailyCount == 1):
        redisExpire(dailyKey, 86400) // 24 saatlik süre ayarla
    
    if (dailyCount > limits.api_calls_per_day):
        return tooManyRequests()
    
    // Eşzamanlı istekleri kontrol et
    concurrentKey = "ratelimit:" + userId + ":concurrent"
    if (!acquireSemaphore(concurrentKey, limits.concurrent_requests)):
        return tooManyRequests()

    try:
        // İsteği işle
        return processRequest(request)
    finally:
        // İşlem bittiğinde semafor serbest bırak
        releaseSemaphore(concurrentKey)
```

3. Yanıt başlıkları:
```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 42
X-RateLimit-Reset: 1609373100
```

4. Ücretsiz kullanıcılar için yükseltme yolu gösterimi:
```json
{
  "error": "Hız limiti aşıldı",
  "message": "Ücretsiz kullanıcılar için API çağrı limitine ulaştınız",
  "upgrade_url": "https://example.com/upgrade",
  "current_tier": "free",
  "next_tier": "premium",
  "next_tier_limit": 60
}
```

**S2: JWT tabanlı hız sınırlama uygularken potansiyel güvenlik açıkları nelerdir ve bunlar nasıl azaltılabilir?**
C: Potansiyel güvenlik açıkları ve azaltma yöntemleri:

1. **Token kurcalama**:
   - Açık: Saldırgan hız limitlerini artırmak için JWT'yi değiştirir
   - Azaltma: Her zaman JWT imzalarını doğrulayın, güçlü gizli anahtarlar veya asimetrik anahtarlar kullanın

2. **Token hırsızlığı**:
   - Açık: Saldırgan yüksek hız limitlerine sahip bir JWT çalar
   - Azaltma: Kısa sona erme süreleri, token parmak izlerini saklama, token iptal etme uygulama

3. **Tekrar saldırıları**:
   - Açık: Geçerli token birden çok kez kullanılır
   - Azaltma: Nonce değerleri dahil etme, token kullanım izleme uygulama

4. **İmzaya kaba kuvvet saldırıları**:
   - Açık: İmzalama anahtarını tahmin etmeye çalışma
   - Azaltma: Güçlü anahtarlar, algoritma rotasyonu, token doğrulama girişimlerini sınırlama

5. **Algoritma değiştirme**:
   - Açık: JWT algoritmasını "none" olarak değiştirme
   - Azaltma: İzin verilen algoritmaları açıkça doğrulama ve zorlama

6. **Birden çok token aracılığıyla hız sınırı atlatma**:
   - Açık: Aynı kullanıcı için birden çok token oluşturma
   - Azaltma: Yalnızca token bazında değil, kullanıcı kimliğine göre merkezi hız sınırlama

7. **Dağıtılmış saldırılar**:
   - Açık: İstekleri birden çok örnek arasında yayma
   - Azaltma: Merkezi hız sınırlama deposu (Redis, dağıtılmış önbellek)

8. **Saat kayması sorunları**:
   - Açık: Sunucu zaman farklılıklarının erken kabul veya reddine neden olması
   - Azaltma: Saat kayması toleransı ekleme, NTP senkronizasyonu

## Langchain SQL Agent ve Kullanımı

**S1: İş kullanıcılarının SQL bilmeden karmaşık bir ilişkisel veritabanını sorgulamasına izin vermek için bir Langchain SQL Agent'ı nasıl tasarlardınız?**
C: Teknik olmayan kullanıcılar için Langchain SQL Agent tasarımı:

1. Şema Anlama Bileşeni:
   - Otomatik veritabanı şema çıkarma
   - Birincil/yabancı anahtar ilişkilerinin tanımlanması
   - İş dostu varlık açıklamaları oluşturma
   - Teknik sütunları iş terimlerine eşleme

2. Doğal Dil İşleme:
   - Farklı sorgu türleri için niyet sınıflandırma
   - Veritabanı nesneleri için varlık çıkarma
   - Bağlam duyarlı sorgu anlama
   - Çok turlu konuşma yeteneği

3. SQL Oluşturma:
   - Doğal dilden optimize edilmiş SQL'e çeviri
   - Yürütmeden önce sorgu doğrulama
   - Güvenli sorgu kısıtlamaları (salt okunur, satır limitleri vb.)
   - Yaygın sorgu desenleri için SQL şablonlama

4. Sonuç Sunumu:
   - Sonuç yapısına dayalı otomatik görselleştirme seçimi
   - Sorgu sonuçlarının doğal dil özetleri
   - Etkileşimli iyileştirme seçenekleri
   - Çeşitli formatlara dışa aktarma yetenekleri

5. Güvenlik ve Yönetim:
   - Rol tabanlı erişim kontrolleri
   - Sorgu denetimi ve günlükleme
   - Hassas bilgiler için veri maskeleme
   - Hız sınırlama ve kaynak yönetimi

Uygulama örneği:
```python
from langchain.agents import create_sql_agent
from langchain.agents.agent_toolkits import SQLDatabaseToolkit
from langchain.sql_database import SQLDatabase
from langchain.llms import OpenAI
from langchain.chat_models import ChatOpenAI
from langchain.chains import SQLDatabaseChain
from langchain.prompts import PromptTemplate

# Veritabanına bağlan
db = SQLDatabase.from_uri("postgresql://username:password@host:port/database")

# İş dostu şema açıklaması oluştur
business_schema_desc = {
    "customers": "Müşterilerimiz hakkında iletişim bilgileri ve hesap durumu dahil bilgiler",
    "orders": "Müşteriler tarafından verilen sipariş emirleri",
    "products": "Fiyatlandırma ve envanter seviyeleri ile ürün kataloğumuz",
    # Daha fazla iş açıklaması ekle
}

# İş bağlamı ile özel istem oluştur
custom_prompt = PromptTemplate(
    input_variables=["input", "table_info", "top_k"],
    template="""
    Şirketimiz hakkındaki iş sorularını SQL sorgularına çeviren yardımcı bir asistansınız.
    
    İş bağlamı:
    - Müşterilere ürünler satan bir perakende şirketiyiz
    - Müşteriler birden çok ürün için sipariş verebilir
    - Envanter seviyelerini ve fiyatlandırmayı takip ediyoruz
    
    Veritabanı şema bilgileri:
    {table_info}
    
    Kullanıcı sorusu: {input}
    
    Önce, kullanıcının ne sorduğunu ve bu bilgilerin hangi tablolarda bulunabileceğini düşünün.
    Ardından, kullanıcının sorusunu yanıtlamak için bir SQL sorgusu yazın. Şunları yaptığınızdan emin olun:
    1. Yalnızca şemada var olan tablo ve sütunları kullanın
    2. İlişkilere dayalı uygun JOIN koşullarını kullanın
    3. Çok sayıda satır döndürürken sonuçları {top_k} ile sınırlayın
    4. Mantık yürütmenizi adım adım açıklayın
    
    SQL Sorgusu:
    """
)

# Özel bileşenlerle SQL zinciri oluştur
sql_chain = SQLDatabaseChain.from_llm(
    llm=ChatOpenAI(temperature=0, model_name="gpt-4"),
    db=db,
    prompt=custom_prompt,
    use_query_checker=True,
    return_intermediate_steps=True,
    verbose=True,
    top_k=10
)

# Sonuç yapısına göre görselleştirme oluşturma işlevi
def generate_visualization(results):
    # En iyi görselleştirmeyi belirleme mantığı
    # Görselleştirme kodu veya açıklaması döndür
    pass

# Ana sorgu işlevi
def business_query(question):
    result = sql_chain(question)
    sql_query = result["intermediate_steps"]["query"]
    query_results = result["intermediate_steps"]["result"]
    
    # Açıklama oluştur
    explanation = f"Sorunuzu şu şekilde anladım: {result['intermediate_steps']['query_checker_explanation']}"
    
    # Uygunsa görselleştirme oluştur
    visualization = generate_visualization(query_results)
    
    return {
        "explanation": explanation,
        "sql_query": sql_query,
        "results": query_results,
        "visualization": visualization
    }

# Örnek kullanım
response = business_query("Geçen ay en çok 5 müşterimiz kaç sipariş verdi?")
```

**S2: LangChain'in SQL Agent'ı, SQL enjeksiyonu ve diğer güvenlik sorunlarını önlemek için oluşturulan SQL sorgularının güvenliğini nasıl doğrulayabilir ve sağlayabilir?**
C: LangChain SQL Agent güvenlik mekanizmaları:

1. **Sorgu Doğrulama**:
   - Sözdizimi hatalarını yakalamak için yürütmeden önce sorguları ayrıştırma
   - Sözdizimi hatalarını yakalamak için veritabanına özel sorgu ayrıştırıcılar kullanma
   - Sorgu yapısı doğrulaması için AST (Soyut Sözdizimi Ağacı) analizi uygulama
   - Referans verilen tüm tablo ve sütunların şemada var olduğunu doğrulama

2. **SQL Enjeksiyonu Önleme**:
   - Tüm kullanıcı tarafından sağlanan değerler için parametre bağlama
   - Dize birleştirme yerine hazırlanmış ifadeler kullanma
   - Sorgu oluşturmadan önce girdi temizleme
   - Bilinen saldırı desenlerine karşı SQL yapısını doğrulama

3. **Ayrıcalık Sınırlamaları**:
   - Sorgu yürütme için salt okunur veritabanı bağlantıları kullanma
   - Minimal izinlere sahip özel veritabanı rolleri oluşturma
   - Mümkün olduğunda satır düzeyinde güvenlik politikaları uygulama
   - Hassas tablolara veya sütunlara erişimi kısıtlama

4. **Sorgu Karmaşıklığı Yönetimi**:
   - Sorgu yürütmede zaman sınırlamaları uygulama
   - Sorgu sonuçları için maksimum satır sınırları belirleme
   - Kaynak yoğun işlemleri tespit etme ve önleme (tam tablo taramaları, kartezyen çarpımlar)
   - Aşırı karmaşık sorguları reddetmek için sorgu maliyet tahmini uygulama

5. **İzleme ve Günlükleme**:
   - Oluşturulan tüm SQL sorgularını günlükleme
   - Yürütme sürelerini ve kaynak kullanımını izleme
   - Olağandışı sorgu desenleri için anomali tespiti uygulama
   - Uyumluluk amaçları için denetim izleri sağlama

Uygulama örneği:
```python
from langchain.agents import create_sql_agent
from langchain.agents.agent_toolkits import SQLDatabaseToolkit
from langchain.sql_database import SQLDatabase
from langchain.llms import OpenAI
import sqlparse
import re

class SafeSQLQueryChecker:
    def __init__(self, db_schema, forbidden_tables=None, read_only=True):
        self.db_schema = db_schema
        self.forbidden_tables = forbidden_tables or ["user_credentials", "payment_info"]
        self.read_only = read_only
        self.dangerous_patterns = [
            r'--',                # SQL yorumu
            r'/\*.*?\*/',         # Çok satırlı yorum
            r';.*?;',             # Çoklu ifadeler
            r'xp_.*?',            # SQL Server genişletilmiş prosedürleri
            r'exec\s+',           # EXEC ifadeleri
            r'WAITFOR\s+DELAY',   # Zamana dayalı saldırılar
            r'UNION\s+ALL\s+SELECT', # UNION tabanlı saldırılar
        ]
    
    def is_safe_query(self, query):
        # Sorguyu normalleştir
        query = query.strip().lower()
        
        # Sorgunun salt okunur olup olmadığını kontrol et
        if self.read_only and not query.startswith("select"):
            return False, "Yalnızca SELECT sorgularına izin verilir"
        
        # Sorguyu ayrıştır
        try:
            parsed = sqlparse.parse(query)[0]
        except Exception as e:
            return False, f"Sorgu ayrıştırılamadı: {str(e)}"
        
        # Tehlikeli desenler için kontrol et
        for pattern in self.dangerous_patterns:
            if re.search(pattern, query, re.IGNORECASE):
                return False, f"Sorgu tehlikeli desen içeriyor: {pattern}"
        
        # Yasaklı tablolar için kontrol et
        for table in self.forbidden_tables:
            if re.search(r'\b' + re.escape(table) + r'\b', query, re.IGNORECASE):
                return False, f"Sorgu yasaklı tabloya referans veriyor: {table}"
        
        # Tablo ve sütun adlarını şemaya göre doğrula
        # ... (şema yapısına bağlı olarak uygulama)
        
        return True, "Sorgu güvenli"


# LangChain SQL Agent'ta örnek kullanım
def create_safe_sql_agent(db_uri, openai_api_key, model_name="gpt-3.5-turbo"):
    # Veritabanına bağlan
    db = SQLDatabase.from_uri(db_uri)
    
    # Şema bilgilerini çıkar
    db_schema = db.get_table_info()
    
    # Sorgu denetleyicisi oluştur
    query_checker = SafeSQLQueryChecker(db_schema)
    
    # LLM oluştur
    llm = OpenAI(temperature=0, openai_api_key=openai_api_key)
    
    # Güvenlik sarmalayıcısı ile toolkit oluştur
    class SafeSQLDatabaseToolkit(SQLDatabaseToolkit):
        def _execute_query(self, query):
            is_safe, reason = query_checker.is_safe_query(query)
            if not is_safe:
                return f"Sorgu güvenlik nedeniyle reddedildi: {reason}"
            return super()._execute_query(query)
    
    toolkit = SafeSQLDatabaseToolkit(db=db)
    
    # Güvenlik toolkit'i ile agent oluştur
    agent = create_sql_agent(
        llm=llm,
        toolkit=toolkit,
        verbose=True
    )
    
    return agent
```
