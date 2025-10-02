## 1. Modern Yazılım Geliştirme Pratikleri

<details>

<summary>Git nedir? GitHub nedir?</summary>

* Git: Yazılım geliştirme sürecinde versiyon kontrolü sağlayan dağıtık bir sistemdir. Kodun farklı sürümlerini takip etmek, değişiklikleri kaydetmek, birden fazla geliştiricinin aynı proje üzerinde eş zamanlı çalışabilmesine olanak tanımaktır.

* GitHub: Git tabanlı projelerin internet üzerinden yönetilebildiği bir platformdur.

</details>

<details>
  
<summary>Temel Git Komutları</summary>

* git init: Çalıştırıldığında klasörde gizli bir dosya oluşturur(.git). Bu dosya projenin sürüm bilgilerini kontrol eder.
  
* git clone: Bir depo veya depo içindeki dosyaların kopyalanıp kendi yerel ağımıza eklenmesi için kullanılır.

* git add: Bir dosyayı seçer ve bir sonraki işleme dahil edilmek üzere hazırlama alanına taşır.
  
* git commit: Depoların belirli zamanlarındaki anlık görüntüsünü oluşturur. Deponun önceki halini ve şimdiki halini anlatmaktadır. Bu komut bir çok veri içerir.( içerik, zaman damgası vs.)
  
* git push: git clone komutu ile benzer çalışır. Kendi git dosyamızı başka bir depo ile paylaşmak için kullanılır.
  
* git pull: Yerel deponuza gelen değişiklikleri günceller. Uzak bir sunucuyla bir depoyla etkileşim kurulduğunda kesinlikle kullanılmalıdır.
  
* git branch: Projenin ana dalından ayrı olarak çalışır ve yapılan değişiklikleri kaydeder.
  
* git merge: Bir projenin dalında yapılan değişikleri ana dal ile birleştirir.
  
</details>

<details>
  
<summary>Merge Conflict</summary>

* Eğer bir dosyanın aynı kısmını, iki farklı dalda iki farklı şekilde değiştirdiyseniz, Git bu değişimlerden hangisini birleştirmeye katacağını bilemeyeceği için, temiz bir birleştirme işlemi yapamaz.
* Çakışan satırları manuel olarak düzenleyip yeniden çalıştırmak gerekir. Yani dosyayı açıp doğru olacak şekilde metni tekrar oluşturup komutlar aracılığı ile birleştirdikten çakışmayı ortadan kaldırırız.

</details>

<details>
  
<summary>CI/CD nedir? Azure DevOps, GitHub Actions ile Pipeline Örnekleri</summary>

* CI (Continuous Integration): Kodların sürekli entegrasyonu. Kod değişiklikleri merkezi repo’ya gönderildiğinde otomatik olarak build edilir ve testlerden geçirilir.

* CD (Continuous Delivery/Deployment): Sürekli teslimat veya dağıtım. Testten geçen kod otomatik olarak sahneleme veya üretme ortamına aktarılır.

* Continuous Delivery: Kod otomatik olarak test edilir, canlıya geçiş manuel onayla yapılır.

* Continuous Deployment: Kod tüm testlerden geçtikten sonra canlıya otomatik olarak alınır.

* Azure DevOps: Yazılım geliştirme ekipleri için araçlar sağlayan bulut tabanlı bir platformdur. İş planlamak, kod üzerinde işlem yapmak, uygulama derlemek için ihtiyacınız olan her şeyi içerir.

* GitHub Actions: Yazılım geliştirme iş akışlarını depomuzda özelleştirebiliriz. CI/CD dahil olmak üzere istediğiniz herhangi bir işi gerçekleştirmek için eylemleri keşfedebilir, oluşturabilir, paylaşabilir ve eylemleri tamamen özelleştirilmiş bir iş akışında birleştirebiliriz.

trigger:
- main   # 'main' branch'e commit atıldığında çalışır

pool:
  vmImage: 'windows-latest'

steps:
- task: UseDotNet@2
  inputs:
    packageType: 'sdk'
    version: '7.x'

- script: dotnet build --configuration Release
  displayName: 'Build project'

- script: dotnet test --no-build --verbosity normal
  displayName: 'Run tests'
  

1. trigger: - main

Bu pipeline, main branch’ine kod gönderildiğinde (push yapıldığında) otomatik çalışır.

Yani geliştirici kodunu GitHub veya Azure Repos üzerinde main branch’ine merge ettiğinde pipeline devreye girer.

2. pool: vmImage: 'windows-latest'

Pipeline, Azure DevOps’un sunduğu hazır sanal makineler üzerinde çalışır.

Burada Windows işletim sistemi kullanılıyor. (İstersen ubuntu-latest de kullanılabilirdi.)

3. UseDotNet@2

Bu adımda .NET SDK kuruluyor.

packageType: sdk → SDK’nın (derleyici ve araçlarla birlikte) kurulmasını sağlar.

version: '7.x' → .NET 7 sürümünü kurar.

 Bu olmadan pipeline, dotnet build komutunu tanımaz.

 4. Build Adımı

Burada uygulama derleniyor (build ediliyor).

configuration Release → uygulamayı Release modunda derler. Bu mod, optimizasyon içerir ve production’a uygun çıktı verir.

displayName → Azure DevOps ekranında adımın ismini daha anlaşılır göstermek için kullanılır.

5. Test Adımı

Derleme sonrası unit testler çalıştırılır.

no-build → build adımını tekrar etmez, sadece testleri çalıştırır.

verbosity normal → test sonuçlarını normal ayrıntı seviyesinde gösterir.

 Bu adım sayesinde kodun bozuk olup olmadığı, testlerden geçip geçmediği pipeline üzerinden otomatik kontrol edilir.


 * GitHub Actions ile Pipeline Örneği


name: .NET Build and Test

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Setup .NET
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: '7.x'

    - name: Restore dependencies
      run: dotnet restore

    - name: Build project
      run: dotnet build --configuration Release --no-restore

    - name: Run tests
      run: dotnet test --no-build --verbosity normal
1) name: — Workflow ismi

Kullanıcı arayüzünde gözüken isimdir (Actions listesinde). Anlaşılır bir isim koymak takibi kolaylaştırır.

2) on: — Ne zaman tetiklenecek?

push → main branch’e direkt push olunca çalışır.

pull_request → main branch’e açılan PR’lar için çalışır (PR üzerinde otomatik kontrol).

Not: Fork üzerinden gelen PR’larda secrets erişimi kısıtlı olabilir (güvenlik nedeniyle).

PR trigger’ı, kodu birleştirmeden önce test edip hataları yakalamak için kullanışlıdır.

3) jobs: ve runs-on: — İş (job) tanımı ve çalıştığı runner

jobs.build bir iş tanımıdır; her job paralel veya birbirinden bağımsız çalışabilir.

runs-on: ubuntu-latest → GitHub’ın sağladığı Linux runner üzerinde çalışır. (İstersen windows-latest ya da macos-latest seçebilirsin; .NET projelerinin platform ihtiyacına göre seç.)

4) steps: — Her bir adımın (step) ne yaptığı

Adımlar iki tip olabilir: uses: (hazır action kullanır) veya run: (doğrudan shell komutu çalıştırır).

5) Hata durumları / sonuçlar nasıl görünür?

Herhangi bir run komutu non-zero (sıfır olmayan) dönüş yaparsa adım başarısız olur ve job sona erer (varsayılan davranış).

GitHub Actions arayüzünde job adımlarını inceleyebilir, step loglarını satır satır görebilirsin. Hangi testin hata verdiği loglarda net çıkar.

Aynı SDK sürümünü kullanmaya dikkat et (localde dotnet --version).

6) Lokal olarak nasıl debug/çalıştırırsın?

Aynı SDK sürümünü kullanmaya dikkat et (localde dotnet --version).

7) Basit iyileştirme önerileri (isteğe bağlı, ama faydalı)

NuGet cache eklemek (yükleme süreleri azalır).

Test artifacts (test sonuçları veya coverage) upload etmek.

Matrix kullanarak farklı .NET sürümleri veya farklı işletim sistemlerinde test çalıştırmak (örnek: strategy.matrix), ama bu biraz ileri seviye.

Action’ları sabit sürüme (örneğin @v3) kullanmak iyi; tam reproduksiyon istersen commit SHA ile pinleyebilirsin.


</details>

<details>
  
<summary>Software Development Life Cycle (SDLC)</summary>

* Planlama

* Analiz

* Geliştirme

* Test

* Dağıtım

* Bakım

* Agile: Çevik metod, hızlı ve esnek geri bildiri.

* Scrum: Kısaca iş yerindeki rollerdir.

* Kanban: İş akışı panosu.
  
</details>

## 2. .NET Ekosistemi

<details>

<summary>.NET nedir? Tarihçesi amacı,neden kullanılır?</summary>

.NET Core, Windows, Linux ve MacOS için yazılım uygulamaları oluşturmak için kullanılabilecek genel amaçlı bir framework’dür. Diğer yazılımların aksine .NET Core, web uygulamaları, mobil uygulamalar, masaüstü uygulamaları, bulut hizmetleri, microservisler, API’ler, oyunlar ve IoT uygulamaları dahil olmak üzere her türlü uygulama/yazılımı oluşturmak için kullanılabilir. Diğer framework’lerden farklı olarak .NET Core, tek bir programlama diliyle sınırlı değildir ve C#, VB.NET, F#, XAML ve TypeScript’i destekler. Bu programlama dilleri açık kaynak kodludur ve bağımsız topluluklar tarafından yönetilir.

.NET Core en gelişmiş, olgun ve kapsamlı class library’leri, ortak API’leri, çoklu dil desteğini ve araçları sunmaktadır. Visual Studio ve Visual Studio Core, .NET Core’u geliştiriciler için en üretken platformlardan biri yapan en gelişmiş ve modern geliştirici IDE’leridir

2002: .NET Framework 1.0 çıktı, C# tanıtıldı (sadece Windows).

2005–2015: Framework 2.0–4.5 arasında LINQ, Async/Await gibi büyük yenilikler geldi.

2016: .NET Core yayınlandı → Açık kaynak, hızlı, platform bağımsz.

2020: .NET 5 ile Framework ve Core birleşti, tek çatı oldu.

2023+: .NET 8  Modern, çoklu platform, yapay zekâ ve bulut odaklı.

</details>

<details>
<summary>.NET Framework, .NET Core ve .NET 7/8+ farkları</summary>
  
|Özellik| .NET Framework   | .NET Core | .NET 7/8+  |
|:-----------------:|:-----------------:|:-----------------:|:-----------------:|
|Platform desteği|Yalnızca Windows'ta çalışır|Platformlar arası çalışır(Linux,Windows,Mac)|Platformlar arası çalışır|
|Güncellemeler|Güncelleme almaz|Güncelleme almaz|Güncelleme almaya devam eder|
|Kaynak kodu|Açık kaynak kodlu değil|Açık kaynak kodlu|Açık kaynak kodu|
|Desteklediği araçlar|Visual Studio|Visual Studio, VS Code, CLI araçları|Visual Studio, VS Code, CLI araçları|
|Kullanım alanları|Eski Windows uygulamaları|Çoklu platform uygulamaları, Web, API, Mikroservis|Modern çoklu platform uygulamaları, Bulut, Web API ve dahası|
|Performans|düşük|Orta|iyi performans|

</details>

<details>

<summary>Senkron ve Asenkron Programlama</summary>

* Senkron Programlama Nedir?

İşlemler ardışık olarak çalışır.

Bir işlem tamamlanmadan diğerine başlanmaz.

Program akışı, her adımda bir önceki adımın sonucunu beklemek zorundadır.

Eğer uzun süren bir işlem varsa (örneğin büyük bir dosyanın okunması, bir web servisinden veri çekilmesi), bu süre boyunca programın geri kalan kısmı çalışmaz, bekler.

* Asenkron Programlama Nedir?

İşlemler paralel veya bağımsız şekilde yürütülebilir.

Bir işlem başlatılır, o işlem arka planda devam ederken program diğer işlere geçebilir.

Program akışı, uzun süren işlemin bitmesini beklemek zorunda kalmaz.

Sonuç hazır olduğunda, program o işin sonucunu alıp kaldığı yerden devam eder.

* Senkron/Asenkron örnek senaryo açıklaması
  
HTTP çağrıları, Web API çağrıları gibi işlemler bekleme gerektirebilir. Geleneksel senkron programlama ile bu işlemleri gerçekleştirmek, aslında lokal bilgisayarın kontrolü dışında olan bir bekleme oluşturur. Bu da hem kullanıcı deneyimi hem de zaman verimliliği açısından oldukça mantıksızdır.

Kullanıcı, arka planda veri çağrıları yapılırken başka işlerle ilgilenebilmek ister. Hiçbirimiz evde bulaşık makinesi çalışıyor diye mutfakta donup kalmayız; makine işini bitirene kadar başka işlerle uğraşırız.

Senkron programlama ile bekleme gerektiren işlemleri çağırmak, bulaşık makinesinin işini bitirmesini oturup beklemek kadar verimsizdir. Asenkron programlama sayesinde ise kullanıcı, çağırdığı bir verinin gelmesini beklerken programın başka bir yerinde farklı bir işlem yapabilir ve ciddi bir zaman kazancı elde eder.

</details>

<details>
  
<summary>dotnet --info çıktısı örneği ve yorumlama</summary>

### Example output of `dotnet --info`

```
.NET SDK:
 Version:           9.0.201
 Commit:            071aaccdc2
 Workload version:  9.0.200-manifests.a3a1a094
 MSBuild version:   17.13.13+1c2026462

Runtime Environment:
 OS Name:     Windows
 OS Version:  10.0.26100
 OS Platform: Windows
 RID:         win-x64
 Base Path:   C:\Program Files\dotnet\sdk\9.0.201\

Workloads installed:
 No installed workloads found.
 Configured to use loose manifests for newly installed manifests.

Host:
  Version:      9.0.3
  Architecture: x64
  Commit:       831d23e561

.NET SDKs installed:
  9.0.201 [C:\Program Files\dotnet\sdk]

.NET runtimes installed:
  Microsoft.AspNetCore.App 8.0.14 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 9.0.3  [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.NETCore.App 8.0.14    [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
  Microsoft.NETCore.App 9.0.3     [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
  Microsoft.WindowsDesktop.App 8.0.14 [C:\Program Files\dotnet\shared\Microsoft.WindowsDesktop.App]
  Microsoft.WindowsDesktop.App 9.0.3  [C:\Program Files\dotnet\shared\Microsoft.WindowsDesktop.App]

Other architectures found:
  x86   [C:\Program Files (x86)\dotnet]
    registered at [HKLM\SOFTWARE\dotnet\Setup\InstalledVersions\x86\InstallLocation]

Environment variables:
  Not set

global.json file:
  Not found
```
dotnet --info komutu, bilgisayarında kurulu olan .NET ortamını özetler. Çıktıdaki bölümler şu anlama gelir:

1. .NET SDK

Hangi SDK sürümünün kullanıldığını gösterir.

SDK (Software Development Kit), .NET projelerini derlemek ve geliştirmek için gerekli araçları içerir.

2. Runtime Environment (Çalışma Zamanı Ortamı)

İşletim sistemini (OS Name, OS Version) ve mimariyi (x64, x86, arm64 vb.) gösterir.

RID (Runtime Identifier) değeri, uygulamanın hangi platformda çalıştığını belirtir.

3. Host

.NET uygulamalarının çalıştırıldığı host ortamının sürümünü gösterir.

Burada .NET runtime versiyonu ve sistem mimarisi listelenir.

4. .NET SDKs installed

Sisteminde yüklü tüm SDK sürümlerini listeler.

Birden fazla SDK kuruluysa, projeler global.json dosyasına veya varsayılan kurallara göre uygun olanı kullanır.

5. .NET runtimes installed

Kurulu olan çalışma zamanlarını listeler.

Microsoft.NETCore.App: Konsol uygulamaları için.

Microsoft.AspNetCore.App: Web uygulamaları için.

Microsoft.WindowsDesktop.App: WinForms ve WPF uygulamaları için.

Farklı sürümlerin aynı anda kurulu olması, eski projeleri de çalıştırabilmeni sağlar.

6. Other architectures found

Ek olarak farklı mimarilerde (örneğin x86) .NET kurulmuşsa burada görünür.

7. Environment variables

.NET ile ilgili özel ortam değişkenleri tanımlandıysa burada listelenir.

8. global.json file

Projede belirli bir SDK sürümü kullanılmak istenirse global.json dosyası ile belirtilir. Eğer bulunmazsa, sistemdeki en güncel SDK kullanılır
</details>

<details>
  
<summary>arrow function (=>) ifadesinin C#’taki yeri</summary>

* Tek satırda fonksiyon tanımlama: static int Multiply(int x, int y) => x * y;
* Lambda ifadesi: Func<int, int> square = n => n * n;

  
</details>

## 3. Backend Geliştirme Temelleri

<details>  

<summary> Backend nedir? Frontend ile farkları </summary>

Front-End, kullanıcı deneyimini ve etkileşimi şekillendirirken, Back-End, web sitesinin işlevselliğini ve veri yönetimini sağlar. her iki katman da başarılı bir web sitesi için kritik öneme sahiptir ve birlikte çalışarak kullanıcıların ihtiyaçlarını karşılayan ve beklentilerini aşan bir deneyim sunarlar.
</details>

<details>    

<summary> Web sunucusu nedir? API nedir? API türleri </summary>

Web sunucusu, istemcilerden (genellikle tarayıcı) gelen HTTP/HTTPS isteklerini kabul eden ve yanıtlayan yazılım veya donanımdır.

Statik içerik (HTML, CSS, JS, resim) ya da dinamik içerik (PHP, ASP.NET, Node.js uygulamaları) dönebilir.

Tarayıcı bir siteye istek gönderdiğinde web sunucusu uygun yanıtı üretip istemciye iletir.

Örnek web sunucuları:

Apache

Nginx

IIS (Internet Information Services)

LiteSpeed

API (Application Programming Interface) Nedir?

API, bir yazılımın başka yazılımlarla haberleşmesini sağlayan arayüzdür.

İç işleyişi bilmeden, belirli kurallar (endpoint, method, veri formatı) üzerinden erişim sağlar.

En yaygın kullanılan türü Web API’lerdir.

Örnek:

Hava durumu uygulaması telefonuna veri getirmek için hava durumu API’sini kullanır.

Google Maps API, başka uygulamalara harita ekleme olanağı sağlar.

API Türleri
***
1. Erişim Türüne Göre

Open API (Public API): Herkesin erişimine açık.

Partner API: Sadece iş ortaklarına özel.

Private API: Sadece kurum içi kullanım için.
***
2. Mimarisine Göre

REST API

HTTP metodlarını (GET, POST, PUT, DELETE) kullanır.

Genellikle JSON döner.

SOAP API

XML tabanlıdır, daha katıdır.

Bankacılık gibi güvenlik kritik alanlarda kullanılır.

GraphQL API

Tek endpoint üzerinden esnek veri sorgusu yapılabilir.

gRPC

Google tarafından geliştirilmiştir.

Hızlı, binary tabanlı, mikroservisler arası iletişim için uygundur.
***
3. Kullanım Alanına Göre

Web API → İnternet üzerinden erişilen API’ler.

Library API → Programlama kütüphaneleri için sağlanan API’ler (.NET, Java API).

Operating System API → İşletim sistemleri ile haberleşmeyi sağlayan API’ler (Windows API, Linux system call).
***
</details>

<details>

<summary>HTTP nedir? HTTP metodları: GET, POST, PUT, DELETE</summary>

GET, POST, PUT, PATCH ve DELETE en sık kullanılanlardır ve HTTP protokolü üzerinden istemciler ile sunucular arasındaki iletişimin temelini oluştururlar. Bu yöntemler, World Wide Web üzerinden istemci-sunucu iletişimi sırasında bir URL ile tanımlanan bir kaynak üzerinde gerçekleştirilebilecek eylemleri tanımlar

HTTP Metodları ve Örnekler
1. GET
* Sunucudan veri istemek için kullanılır.

* Gövde (body) göndermez.
```
GET /products HTTP/1.1
Host: example.com
```
Sunucudan ürünler listesini ister.

2. POST

* Sunucuya yeni veri eklemek/göndermek için kullanılır.

* Veri body kısmında gönderilir.
```
POST /products HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "name": "Laptop",
  "price": 20000
}
```
Sunucuya yeni ürün ekler.


3. PUT

* Sunucuda var olan bir veriyi tamamen güncellemek için kullanılır.

* Gönderilen body, önceki verinin yerine geçer.
```
PUT /products/10 HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "name": "Gaming Laptop",
  "price": 25000
}
```
ID’si 10 olan ürünün tüm verisini günceller.


4. PATCH

* Sunucudaki veriyi kısmi olarak güncellemek için kullanılır.

* Sadece değişen alanlar body’de gönderilir.
```
PATCH /products/10 HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "price": 22000
}
```
Sadece fiyat bilgisini günceller.

5. DELETE

* Sunucudaki veriyi silmek için kullanılır.
```
DELETE /products/10 HTTP/1.1
Host: example.com
```
ID’si 10 olan ürünü siler.


6. HEAD

* GET gibidir ama sadece başlık (header) bilgilerini döner, gövdeyi döndürmez.
```
HEAD /products HTTP/1.1
Host: example.com
```
7. OPTIONS

* Sunucunun hangi HTTP metodlarını desteklediğini öğrenmek için kullanılır.
```
OPTIONS /products HTTP/1.1
Host: example.com
```

Cevap:
Allow: GET, POST, PUT, DELETE, OPTIONS


Özet:

GET → Veri al

POST → Yeni veri oluştur

PUT → Tam güncelle

PATCH → Kısmi güncelle

DELETE → Sil

HEAD → Sadece header bilgilerini al

OPTIONS → Desteklenen metodları öğren
  
</details>

<details>

<summary>RESTful servislerin çalışma mantığı</summary>

* REST Nedir?

Representational State Transfer (REST), web servisleri için kullanılan bir mimari tarzdır.

Amacı, HTTP protokolünün sunduğu kuralları kullanarak basit, anlaşılır ve standart bir şekilde istemci–sunucu arasında iletişim kurmaktır.

REST’i “kurallar bütünü” olarak düşünebilirsin.

* Temel Mantık

RESTful servislerde:

Her kaynak (örneğin: kullanıcı, ürün, sipariş) bir URL (endpoint) ile temsil edilir.

Bu kaynaklar üzerinde işlem yapmak için HTTP metodları (GET, POST, PUT, DELETE, PATCH…) kullanılır.

Veri genelde JSON formatında taşınır.

* Kaynak ve URI Mantığı

Her şey bir kaynaktır.

Örnek: Kullanıcı listesi → /users

Tek bir kullanıcı → /users/5

Kullanıcının siparişleri → /users/5/orders

URI, kaynağı temsil eder. Kaynağın ne olduğunu URI belirler, üzerinde ne yapılacağını HTTP metodu belirler.

* HTTP Metodları ile İşlemler

REST’te CRUD işlemleri HTTP metodlarına denk gelir:

GET /users → Tüm kullanıcıları getir

GET /users/5 → ID’si 5 olan kullanıcıyı getir

POST /users → Yeni kullanıcı oluştur

PUT /users/5 → ID’si 5 olan kullanıcıyı tamamen güncelle

PATCH /users/5 → ID’si 5 olan kullanıcıyı kısmi güncelle

DELETE /users/5 → ID’si 5 olan kullanıcıyı sil

* İstemci - Sunucu İlişkisi

İstemci (client) → Tarayıcı, mobil uygulama, masaüstü program olabilir.

Sunucu (server) → İstekleri alır, işler ve yanıt verir.

İstemci, sunucuya hangi veriyle ne yapılacağını HTTP isteği (request) ile söyler.

Sunucu, sonucu HTTP cevabı (response) olarak döner.

* Statelesness (Durumsuzluk)

RESTful servisler stateless olmalıdır.

Sunucu, her isteği bağımsız görür.

Önceki isteklerin bilgisini tutmaz.

Gerekli bilgiler (kimlik doğrulama, filtreler, parametreler) her istekte tekrar gönderilir.

* Response ve Status Codes

Sunucu her isteğe bir HTTP durum kodu döner:

200 OK → İşlem başarılı

201 Created → Yeni kaynak oluşturuldu

400 Bad Request → Hatalı istek

401 Unauthorized → Yetkisiz erişim

404 Not Found → Kaynak bulunamadı

500 Internal Server Error → Sunucu hatası

* JSON Örneği

Bir kullanıcı oluşturma isteği:
```
POST /users HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "name": "Ali",
  "email": "ali@example.com"
}
```

Sunucu cevabı:
```
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": 5,
  "name": "Ali",
  "email": "ali@example.com"
}
```
</details>

<details> 

<summary>JSON veri formatı ve kullanım amacı</summary>
JSON Veri Formatı

JSON (JavaScript Object Notation), verilerin metin tabanlı olarak saklanmasını ve sistemler arasında kolayca taşınmasını sağlayan bir veri değişim formatıdır.

Özellikleri

Anahtar-değer çiftlerinden oluşur.

Diziler ve nesneler kullanılarak hiyerarşik yapı kurulabilir.

Programlama dillerinden bağımsızdır.

Hem insanlar hem makineler tarafından kolay okunabilir.

Kullanım Amacı

Web istemcisi ile sunucu arasında veri alışverişi yapmak.

Uygulamalarda yapılandırma (config) dosyası olarak kullanmak.

Farklı platform ve diller arasında veri taşımak.

Küçük, anlaşılır veri depolama ihtiyaçlarını karşılamak.

Örnek JSON Verisi
```
{
  "ad": "Ali",
  "yas": 30,
  "ogrenciMi": true,
  "dersler": ["Matematik", "Tarih", "Fizik"],
  "adres": {
    "sehir": "Bursa",
    "postaKodu": 16000
  }
}
```
Örneğin Açıklaması

* "ad": "Ali" → Anahtar ad, değeri "Ali" olan bir metin bilgisidir.

* "yas": 30 → Anahtar yas, değeri 30 olan sayısal bir bilgidir.

* "ogrenciMi": true → Anahtar ogrenciMi, değeri true olan mantıksal (boolean) bilgidir.

* "dersler": ["Matematik", "Tarih", "Fizik"] → dersler anahtarı, içinde üç eleman bulunan bir diziyi ifade eder.

* "adres": { "sehir": "Bursa", "postaKodu": 16000 } → adres anahtarı, içinde başka anahtar-değer çiftleri olan bir nesneyi temsil eder.

</details>

<details>
<summary>SOAP ve GraphQL nedir, REST’ten farkları</summary>

* SOAP (Simple Object Access Protocol)

XML tabanlı bir iletişim protokolüdür.

Katı standartlara bağlıdır (WS-Security, WS-ReliableMessaging gibi).

Yalnızca XML kullanır.

HTTP dışında SMTP, TCP gibi farklı protokollerle çalışabilir.

Daha çok bankacılık, finans, telekom gibi güvenliğin ve standartlaşmanın çok kritik olduğu alanlarda tercih edilir.

* GraphQL

Facebook tarafından geliştirilmiş, API’ler için bir sorgulama dilidir.

Tek endpoint üzerinden çalışır.

İstemci yalnızca ihtiyacı olan veriyi talep eder.

JSON formatında yanıt döner.

Over-fetching (gereğinden fazla veri çekme) ve under-fetching (eksik veri çekme) sorunlarını çözer.

Gerçek zamanlı veri için Subscriptions özelliği vardır.

* REST (Representational State Transfer)

HTTP tabanlı, en yaygın kullanılan web servis mimarisidir.

Kaynaklara HTTP metodları (GET, POST, PUT, DELETE) ile erişilir.

JSON ya da XML dönebilir (günümüzde çoğunlukla JSON).

Her kaynak için ayrı endpoint vardır (örneğin /users, /orders).

Basit, anlaşılır ve yaygın kullanımda olan bir yaklaşım sunar.

| Özellik             | SOAP                            | REST                           | GraphQL                           |
|---------------------|---------------------------------|--------------------------------|-----------------------------------|
| Veri formatı        | XML                             | JSON (çoğunlukla) / XML        | JSON                              |
| Endpoint yapısı     | Tek endpoint, karmaşık yapı     | Her kaynak için ayrı endpoint   | Tek endpoint                      |
| Esneklik            | Katı standartlı                 | Orta düzey                     | Çok esnek (yalnızca istenen veri) |
| Performans          | Ağır, yavaş                     | Hafif, genellikle hızlı         | Daha verimli                      |
| Protokol desteği    | HTTP, SMTP, TCP vb.             | HTTP                           | HTTP                              |
| Kullanım alanı      | Bankacılık, kurumsal entegrasyon| Genel web servisleri            | Modern web ve mobil API’ler       |
| Öne çıkan özellik   | Güvenlik ve standartlar         | Basitlik ve yaygınlık           | Esneklik ve özelleştirme          |  

</details>

## 4. ASP.NET

<details> 

<summary>ASP.NET ve ASP.NET Core nedir? Avantajları, farkları</summary>

ASP.NET ve ASP.NET Core, Microsoft tarafından geliştirilen web uygulama geliştirme framework’leridir. İkisi birbirine yakın kavramlar olsa da, teknoloji olarak farklı dönemleri temsil ederler.

* ASP.NET Nedir?

2002’de .NET Framework’ün bir parçası olarak tanıtıldı.

Yalnızca Windows işletim sistemi üzerinde çalışır.

Web Forms, MVC (Model-View-Controller) ve Web API gibi yaklaşımları destekler.

.NET Framework’e bağımlıdır, yani günümüzde .NET Framework 4.8’e kadar gelmiştir ve daha fazla büyük güncelleme almayacaktır.

Modern uygulamalarda “eski teknoloji” kabul ediliyor ama hâlen kurumsal projelerde yaygın olarak kullanılmakta.

* ASP.NET Core Nedir?

2016’da tanıtıldı, ASP.NET’in yeniden yazılmış, açık kaynak ve çapraz platform halidir.

Windows, Linux ve macOS üzerinde çalışır.

.NET Core üzerine kurulmuştu, şimdi .NET 5+ (günümüzde .NET 9) çatısı altında gelişiyor.

Daha hızlı, hafif ve bulut tabanlı uygulamalara uygun.

Web API ve MVC birleşmiş durumda → tek bir yapıdan hem web sayfası hem API geliştirilebilir.

Dependency Injection, Middleware, Minimal API gibi modern mimari özellikleri destekler.

* ASP.NET Avantajları

Uzun yıllardır kullanılan, olgun bir framework.

Kurumsal projelerde hâlâ çok geniş kullanım alanı var.

Windows Server + IIS entegrasyonu güçlü.

Büyük topluluk ve çok sayıda hazır kütüphane mevcut.

* ASP.NET Core Avantajları

Çapraz platform (Windows, Linux, macOS).

Performansı yüksek, özellikle Kestrel web server sayesinde.

Açık kaynak ve sürekli gelişiyor.

Bulut dostu, container (Docker, Kubernetes) ile kolayca dağıtılabilir.

Daha esnek: Middleware tabanlı mimari → geliştirici kendi pipeline’ını kurabilir.

Minimal API ve gRPC desteği sayesinde mikroservislerde ideal.

| Özellik          | ASP.NET                      | ASP.NET Core                               |
|------------------|------------------------------|--------------------------------------------|
| **Çıkış Yılı**   | 2002                         | 2016                                       |
| **Çalışma Ortamı** | Sadece Windows              | Windows, Linux, macOS                     |
| **Performans**   | Daha düşük, IIS bağımlı      | Yüksek, Kestrel + IIS/NGINX                |
| **Framework**    | .NET Framework               | .NET 5/6/7/8/9                             |
| **Mimari**       | Web Forms, MVC, Web API ayrı | MVC + API birleşik, Middleware tabanlı     |
| **Açık Kaynak**  | Hayır                        | Evet                                       |
| **Gelecek**      | Yeni geliştirme yok          | Aktif olarak geliştiriliyor                |

</details>

<details> 

<summary>MVC nedir, ne için kullanılınır</summary>

MVC (Model-View-Controller), yazılım geliştirmede kullanılan bir mimari desendir.

* Model (M): Uygulamanın verilerini ve iş kurallarını temsil eder. (Örn: Veritabanı işlemleri, iş mantığı)

* View (V): Kullanıcıya gösterilen arayüzdür. (Örn: HTML sayfası, UI ekranı)

* Controller (C): Kullanıcıdan gelen isteği alır, işleyip uygun Model ve View’i yönlendirir.

Ne için kullanılır?
Uygulamalarda katmanlı yapı sağlar. Kodun okunabilirliğini, bakımını ve test edilebilirliğini kolaylaştırır. Web uygulamalarında en çok tercih edilen desenlerden biridir.

</details>

<details>
  
<summary>Middleware nedir, nasıl çalışır</summary>

* Middleware, farklı uygulamalar, sistemler ve veritabanları arasında etkileşim ve veri akışı sağlayan bulut hizmetleridir. Uygulamalar ve veriler arasında bir köprü görevi görür bu sayede sistemler arasında kesintisiz iletişim sağlar ve veri akışını düzenler.
ASP.NET Core uygulamalarında middleware, gelen HTTP isteğinin geçtiği aşamaları belirleyen işlem hattıdır. Her middleware isteği işleyebilir, sonraki aşamaya gönderebilir veya sonlandırabilir. Bu yüzden eklenme sırası çok önemlidir. Yanlış sıralama, kimlik doğrulama ve yetkilendirme gibi kritik işlevlerin devre dışı kalmasına yol açar.

Doğru Sıralama Örneği
```
app.UseRouting();          
app.UseAuthentication();   
app.UseAuthorization();    
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
});
```
Özetle

* UseRouting() → Rotaları hazırlar.

* UseAuthentication() → Kullanıcıyı doğrular.

* UseAuthorization() → Yetki kontrolü yapar.

* UseEndpoints() → İsteği ilgili endpoint’e yönlendirir.
  
</details>

<details>

<summary>Dependency Injection (DI) nedir, neden önemlidir</summary>

Dependency Injection (DI) Nedir?
Dependency Injection (Bağımlılık Enjeksiyonu), bir sınıfın ihtiyaç duyduğu bağımlılıkları (örneğin başka sınıflar, servisler, veritabanı bağlantıları) kendisi oluşturmaması, dışarıdan alması prensibidir. Yani bir sınıf içinde new anahtar kelimesiyle bağımlılık oluşturmak yerine, bu bağımlılıklar constructor, property veya method aracılığıyla dışarıdan “enjekte edilir”.

 * Örnek:
```
// Kötü örnek (sınıf bağımlılığı kendi oluşturuyor)
public class OrderService
{
    private readonly PaymentService _paymentService = new PaymentService();

    public void CompleteOrder()
    {
        _paymentService.Pay();
    }
}

// İyi örnek (Dependency Injection kullanılıyor)
public class OrderService
{
    private readonly IPaymentService _paymentService;

    public OrderService(IPaymentService paymentService)
    {
        _paymentService = paymentService;
    }

    public void CompleteOrder()
    {
        _paymentService.Pay();
    }
```  
</details>

<details> 
  
<summary>Clean Architecture</summary>

Clean Architecture Özeti

Amaç:
Uygulamanın iş kurallarını (domain) framework, veritabanı, UI gibi dış detaylardan bağımsız hale getirmek. Böylece kod esnek, test edilebilir ve sürdürülebilir olur.

Katmanlar

* I. Domain

İş kuralları, entity’ler ve değer nesneleri.

Hiçbir dış katmana bağımlı değil.

* II. Application

Use case’ler (iş akışları).

Domain’i kullanarak iş senaryolarını yürütür.

Dış sistemlerle konuşmak için sadece interface tanımlar.

* III. Infrastructure

Teknik detaylar (veritabanı, servis entegrasyonları).

Application’daki interface’lerin implementasyonu burada yapılır.

* IV. API (Presentation)

Kullanıcıya/istemciye açılan uç noktalar (controller, endpoint).

Request alır → Application’a iletir → Response döner.

* Bağımlılıkların Dışa Akması İlkesi

Bağımlılıklar içeriye doğru akar.

Domain hiçbir şeye bağımlı değil.

Application sadece Domain’e bağımlı.

API ve Infrastructure, Application ve Domain’e bağımlı

</details>

<details>  

<summary>Katmanlı Mimari (Layered Architecture)</summary>
Katmanlar

* I. Presentation (UI) Katmanı

Kullanıcı ile etkileşim sağlar.

Controller, View veya API endpoint’leri burada bulunur.

İş mantığını içermez, sadece Business katmanını çağırır.

* II. Business (Logic) Katmanı

İş kuralları ve uygulama mantığı burada yer alır.

Presentation’dan gelen istekleri işler, gerekirse Data Access katmanını kullanır.

Service sınıfları genellikle bu katmanda bulunur.

* III. Data Access (DAL) Katmanı

Veritabanı veya dış veri kaynaklarıyla etkileşim kurar.

Repository sınıflarıyla CRUD işlemleri yapılır.

Business katmanındaki servisler, repository üzerinden veri çeker/kaydeder.

Service & Repository Pattern

Repository Pattern: Veritabanı erişimini soyutlar. (IUserRepository, EfUserRepository)

Service Pattern: İş mantığını barındırır ve repository’leri kullanarak senaryoları yürütür. (UserService)

 Akış: UI → Service → Repository → Database

![1_vNZs7q1OgPc2yDaiGJpCwg](https://github.com/user-attachments/assets/4be9759b-39ae-4855-87ed-25ab931897fd)

</details>

## 5. Veritabanı ve ORM

<details>

<summary>SQL nedir?</summary>

SQL, ilişkisel veri tabanlarını yönetmek, SQL veri tabanları oluşturmak ve farklı işlevler gerçekleştirerek içlerindeki verileri manipüle etmek için standartlaştırılmış bir programlama dilidir.

Hem veri tabanı yöneticileri hem de geliştiriciler SQL’i verileri manipüle etmek ve veri entegrasyon komut dosyaları yazmak için kullanır. Benzer şekilde, veri analistleri de ilişkisel bir veri tabanını derinlemesine analiz etmek için SQL kullanır.

*	4 temel SQL sorgusuna örnek

```
-- 1. SELECT → Veri listeleme
SELECT * FROM Customers;

-- 2. INSERT → Yeni veri ekleme
INSERT INTO Customers (Name, City) VALUES ('Emirhan', 'Bursa');

-- 3. UPDATE → Veri güncelleme
UPDATE Customers SET City = 'İstanbul' WHERE Name = 'Emirhan';

-- 4. DELETE → Veri silme
DELETE FROM Customers WHERE Name = 'Emirhan';
```

</details>

<details>

<summary>İlişkisel ve ilişkisel olmayan veri tabanları arasındaki farklar</summary>

1. Veri Yapısı

* İlişkisel Veritabanı (RDBMS):
  Veriler tablolarda (satır–sütun yapısında) saklanır.
  Örn: MySQL, PostgreSQL, Oracle.

* İlişkisel Olmayan Veritabanı (NoSQL):
  Veriler esnek yapılarda saklanır. (Doküman, key-value, grafik, kolon tabanlı)
  Örn: MongoDB, Redis, Cassandra, Neo4j.

***

2. Şema (Schema)

* RDBMS: Katı şemaya sahiptir. Tablo yapısı önceden tanımlanmalıdır.

* NoSQL: Şemasız veya esnek şemalıdır. Veri yapısı dinamik olarak değiştirilebilir.

***

3. Sorgulama

* RDBMS: Veri sorgulamak için SQL kullanılır.

* NoSQL: Her veritabanı kendi sorgulama yöntemine sahiptir (ör. JSON tabanlı sorgular, API çağrıları).

***

4. Tutarlılık

* RDBMS: ACID (Atomicity, Consistency, Isolation, Durability) kurallarını takip eder. Yani veri bütünlüğü çok güçlüdür.

* NoSQL: Genellikle BASE (Basically Available, Soft state, Eventual consistency) modelini benimser. Yüksek ölçeklenebilirlik için bazen anlık tutarlılıktan ödün verir.

***

5. Ölçeklenebilirlik

* RDBMS: Genelde dikey ölçeklenir (daha güçlü donanım eklenir).

* NoSQL: Yatay ölçeklenir (yeni sunucular eklenerek genişler).

***

6. Kullanım Alanları

* RDBMS: Bankacılık, muhasebe, e-ticaret gibi güçlü veri tutarlılığı gerektiren sistemlerde.

* NoSQL: Sosyal medya, gerçek zamanlı analiz, IoT, büyük veri gibi esneklik ve hız isteyen sistemlerde.
 
</details>

<details> 

<summary>ORM nedir? Entity Framework Core nedir?</summary>

ORM (Object Relational Mapping), yazılım geliştirmede kullanılan bir tekniktir.

ORM, nesne tabanlı programlama dillerinde kullanılan sınıf ve nesneler ile veritabanındaki tablolar ve kayıtlar arasında bir köprü kurar. Yani veritabanı işlemlerini SQL sorguları yazmadan, doğrudan programlama dilindeki nesneler üzerinden yapmanıza olanak tanır.
***
Örnek:

* Veritabanında Users tablosu varsa, kod tarafında bunun bir User sınıfı olur.

* user.Name = "Ali"; gibi bir işlem yaptığınızda ORM bunu SQL sorgusuna çevirip veritabanına kaydeder.

Avantajları:

* SQL sorgularını elle yazmaya gerek kalmaz, hata riski azalır.

* Kod okunabilirliği artar.

* Veritabanı bağımlılığı azalır (farklı veritabanlarına geçiş daha kolay olur).

Kullanılan bazı ORM örnekleri:

* .NET dünyasında: Entity Framework, Dapper (yarı ORM)

* Java dünyasında: Hibernate

* Python’da: SQLAlchemy, Django ORM

***

* Entity Framework Core Nedir?

Entity Framework Core, son sürümü 9.0.9 olarak yayınlanmış, Microsoft tarafından geliştirilen ve .NET Framework ve .NET Core ile birlikte kullanılabilen bir ORM aracıdır. Entity Framework Core (EF Core) ve Entity Framework (EF 6.x ve önceki sürümler) arasında önemli farklar vardır. EF Core daha hafif ve performanslıdır ve veritabanı platformları için çoklu platform desteği sunar.

</details>

<details>

<summary>DbContext nedir, nasıl kullanılır?</summary>

 1. * DbContext Nedir?

 EF Core’un ana sınıfıdır ve veritabanına erişimi yönetir.

 Veritabanındaki tablolarla C# sınıflarını eşler (Entity’ler).

 CRUD işlemleri (Create, Read, Update, Delete) için bir ara katman sağlar.

 Veritabanı bağlantısını, sorguları ve değişiklik takibini yönetir.

 2. * Temel Özellikleri

 DbSet<TEntity>: Her tablo için bir DbSet oluşturulur. Bu, o tabloya erişimi sağlar.

 Change Tracker: Entity üzerinde yapılan değişiklikleri takip eder ve SaveChanges() ile veritabanına yazar.

 LINQ sorguları: DbContext üzerinden LINQ ile veri çekebilirsiniz.

* Nasıl Kullanılınır Örnek:
  
```
using var context = new AppDbContext();

// Veri ekle
context.Products.Add(new Product { Name="Laptop", Price=12000 });
context.SaveChanges();

// Veri oku
var all = context.Products.ToList();

// Güncelle
var p = context.Products.First();
p.Price = 11000;
context.SaveChanges();

// Sil
context.Products.Remove(p);
context.SaveChanges();
```
</details>

<details> 

<summary>LINQ nedir? En çok kullanılan LINQ ifadeleri</summary> 

* LİNQ ''(Language Integrated Query)'' Nedir

LINQ (Language Integrated Query — Dil ile Tümleşik Sorgu); veri koleksiyonlarını sorgulamak, verileri filtrelemek veya dönüştürmek gibi işlemleri kolaylaştıran güçlü bir teknolojidir. C# 3.0 sürümü ile kullanıma başlamıştır. LINQ, C# ve diğer .NET programlama dillerinde kullanılarak veri kaynakları üzerinde sorgular oluşturmayı, veriye erişimi, filtrelemeyi, sıralamayı, gruplamayı, birleştirmeyi ve dönüştürmeyi sağlar.

* En çok kullanılan LİNQ ifadeleri

1. Where

* Koşula göre filtreleme yapar.
```
int[] numbers = { 1, 2, 3, 4, 5 };
var evenNumbers = numbers.Where(n => n % 2 == 0);
```
2. Select

* Veriyi dönüştürmek için kullanılır.
```
var squares = numbers.Select(n => n * n);
```
3. OrderBy / OrderByDescending

* Veriyi artan veya azalan sıraya göre sıralar.
```
var ordered = numbers.OrderBy(n => n);
var descending = numbers.OrderByDescending(n => n);
```
4. First / FirstOrDefault

* Koşula uyan ilk elemanı döndürür.
```
var firstEven = numbers.First(n => n % 2 == 0);
var firstEvenOrDefault = numbers.FirstOrDefault(n => n % 2 == 0);
```
5. Last / LastOrDefault

* Koşula uyan son elemanı döndürür.
```
var lastEven = numbers.Last(n => n % 2 == 0);
```
6. Single / SingleOrDefault

* Sadece bir elemanın döndüğü durumlar için kullanılır.
```
var singleItem = numbers.Single(n => n == 3);
```
7. Count

* Eleman sayısını verir.
```
var evenCount = numbers.Count(n => n % 2 == 0);
```
8. Any

* Koşula uyan en az bir eleman var mı kontrol eder.
```
bool hasEven = numbers.Any(n => n % 2 == 0);
```
9. All

* Tüm elemanların koşulu sağlayıp sağlamadığını kontrol eder.
```
bool allPositive = numbers.All(n => n > 0);
```
10. Distinct

* Tekrarlayan elemanları kaldırır.
```
int[] nums = { 1, 2, 2, 3, 3, 3 };
var unique = nums.Distinct();
```

</details>

<details>

<summary>LİNQ örnekleri ve SQL açıklamaları</summary>

1. * Where – Filtreleme

* LINQ:
```
var evenNumbers = numbers.Where(n => n % 2 == 0);
```

Açıklama: Dizideki çift sayıları seçer.
SQL Karşılığı:
```
SELECT * FROM Numbers WHERE Value % 2 = 0;
```
2. * Select – Dönüştürme

* LINQ:
```
var squares = numbers.Select(n => n * n);
```

Açıklama: Her sayının karesini alır.
SQL Karşılığı:
```
SELECT Value * Value AS Square FROM Numbers;
```
3. * OrderBy – Sıralama

* LINQ:
```
var ascending = numbers.OrderBy(n => n);
```

Açıklama: Sayıları artan sıraya göre sıralar.
SQL Karşılığı:
```
SELECT * FROM Numbers ORDER BY Value ASC;
```
4. * GroupBy – Gruplama

* LINQ:
```
var grouped = numbers.GroupBy(n => n % 2 == 0 ? "Even" : "Odd");
```

Açıklama: Sayıları çift ve tek olarak gruplar.
SQL Karşılığı:
```
SELECT CASE WHEN Value % 2 = 0 THEN 'Even' ELSE 'Odd' END AS GroupKey,
       COUNT(*) 
FROM Numbers
GROUP BY CASE WHEN Value % 2 = 0 THEN 'Even' ELSE 'Odd' END;
```
5. * Join – İki koleksiyonu birleştirme

* LINQ:
```
var studentGrades = students.Join(grades,
                                  s => s.Id,
                                  g => g.StudentId,
                                  (s, g) => new { s.Name, g.Grade });

```
Açıklama: Öğrenciler ve notlarını birleştirir.
SQL Karşılığı:
```
SELECT s.Name, g.Grade
FROM Students s
INNER JOIN Grades g ON s.Id = g.StudentId;
```
 
</details>

<details>

<summary>Code-First ve Database-First yaklaşımı nedir?	</summary>

* Code-First

Önce uygulamanın model sınıflarını (entity class) kod tarafında oluşturursunuz. EF bu sınıfları temel alarak veritabanını kendisi üretir ve migration ile güncelleyebilirsiniz. Veritabanı tasarımını geliştirici kontrol eder.
Genelde yeni projelerde veya veritabanı tasarımını sıfırdan yapmak istediğiniz durumlarda tercih edilir.

* Database-First

Önceden hazırlanmış bir veritabanınız vardır. EF bu veritabanındaki tabloları, alanları ve ilişkileri otomatik olarak class’lara dönüştürür (reverse engineering). Kod tarafında model oluşturma ihtiyacı azalır.
Mevcut bir veritabanını kullanan projelerde veya veritabanı tasarımının DBA tarafından yapıldığı durumlarda tercih edilir.

* Code-First vs DB-First karşılaştırması

| Özellik               | Code-First                          | Database-First                     |
|-----------------------|------------------------------------|-----------------------------------|
| Başlangıç Noktası      | Kod tarafında modeller oluşturulur | Mevcut veritabanı kullanılır       |
| Veritabanı Oluşturma   | EF modellerden veritabanı üretir   | EF veritabanından modeller üretir |
| Değişiklik Yönetimi    | Migration ile yönetilir            | DB’de manuel değişiklik gerekir    |
| Esneklik               | Geliştiriciye daha fazla esneklik  | Veritabanı tasarımına bağımlıdır   |
| Kullanım Senaryosu     | Yeni projelerde                   | Mevcut veritabanı olan projelerde |
 
</details>

<details>

<summary>Temel SQL sorguları: SELECT, INSERT, UPDATE, DELETE</summary>

1.SELECT

Veritabanından veri okumak için kullanılır.
```
SELECT * FROM Employees;  -- Tüm kolonları getirir
SELECT Name, Salary FROM Employees WHERE Salary > 5000;  -- Şartlı veri
```
2.INSERT

Tabloya yeni kayıt eklemek için kullanılır.
```
INSERT INTO Employees (Name, Position, Salary)
VALUES ('Ali', 'Developer', 7500);
```
3.UPDATE

Mevcut kayıtları güncellemek için kullanılır.
```
UPDATE Employees
SET Salary = 8000
WHERE Name = 'Ali';
```
4.DELETE

Tablodan veri silmek için kullanılır.
```
DELETE FROM Employees WHERE Name = 'Ali';
```


</details>

## 6. Güvenlik ve Performans

<details>

<summary>Authentication vs Authorization nedir?</summary>

 * Authentication vs Authorization Nedir?

| Özellik | Authentication (Kimlik Doğrulama) | Authorization (Yetkilendirme) |
|---------|-----------------------------------|-------------------------------|
| Tanım   | Kullanıcının kim olduğunu doğrular | Kullanıcının hangi kaynaklara veya işlemlere erişebileceğini belirler |
| Amaç    | Kimlik doğrulamak | Yetki kontrolü yapmak |
| Ne zaman? | Sisteme giriş esnasında | Giriş yaptıktan sonra |
| Örnek   | Kullanıcı adı ve şifre girmek | Admin paneline erişim izni kontrolü |
 
</details>

<details>

<summary>JWT (JSON Web Token) nedir, nasıl çalışır?</summary>

* JSON Web Token (JWT), tarafların birbirleri arasındaki veri alışverişini ve bunun doğrulamasını sağlayan JSON tabanlı RFC 7519'de tanımlanmış açık bir standarttır. Örneğin bir sunucu, kullanıcının yönetici ayrıcalıklarına sahip olduğunu belirten bir anahtar (token) oluşturabilir ve bunu kullanıcıya gönderebilir. Kullanıcı daha sonra bu anahtar ile kendisine tanımlanmış olan yönetici yetkisini bir istemcide kullanabilir ve bütün taraflar tarafından yetkisi doğrulanabilir

* JWT yapısının temel bileşenleri
  
JWT (JSON Web Token) üç ana bileşenden oluşur ve bu bileşenler nokta (.) ile birbirinden ayrılır:

Header.Payload.Signature

1. Header (Başlık)

* Token tipini ve kullanılan imzalama algoritmasını belirtir.

* JSON formatındadır.

Örnek:
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```
2. Payload (Yük)

* Kullanıcıya veya oturuma ait verileri (claims) içerir.

* Kimlik bilgileri, roller, token geçerlilik süreleri gibi bilgiler burada yer alır.

Örnek:
```
{
  "sub": "1234567890",
  "name": "emco",
  "admin": true,
  "exp": 1712345678
}
```
3. Signature (İmza)

* Header ve Payload Base64Url formatında encode edilir, ardından gizli bir anahtar ile (HMAC SHA veya RSA gibi algoritmalarla) imzalanır.

* Token’ın değiştirilip değiştirilmediğini doğrular.

 
</details>

<details>  

<summary>OAuth, OAuth2.0, OpenIddict, OpenID nedir? Aralarındaki ilişki</summary>

# OAuth, OAuth 2.0, OpenID, OpenID Connect ve OpenIddict Nedir?

Günümüzde web ve mobil uygulamalarda **kimlik doğrulama (authentication)** ve **yetkilendirme (authorization)** kritik bir ihtiyaçtır. Bu noktada farklı standartlar ve kütüphaneler devreye girer. Bu yazıda **OAuth**, **OAuth 2.0**, **OpenID**, **OpenID Connect** ve **OpenIddict** kavramlarını ve aralarındaki ilişkiyi inceleyeceğiz.

## OAuth (1.0)

* 2007’de geliştirilen ilk standarttır.
   
* Kullanıcıların bir uygulamadaki (kaynak sunucusu) verilerine başka bir uygulamanın erişmesine izin verir.  

* Yalnızca yetkilendirme sağlar, kimlik doğrulama yapmaz.  

* Karmaşık yapısı nedeniyle zamanla OAuth 2.0’a bırakmıştır.

## OAuth 2.0

* OAuth’un modern ve esnek versiyonudur.  

* Yetkilendirme çerçevesidir, kimlik doğrulama yapmaz.  

* Access Token ve Refresh Token kavramlarını getirir.  

* Google, Microsoft, Facebook API erişimlerinde standart olarak kullanılır.

## OpenID

* Kullanıcıların tek bir kimlik sağlayıcısı (örneğin Google veya Yahoo) ile farklı sitelere giriş yapmasını sağlayan eski bir kimlik doğrulama protokolüdür.
 
* Sadece kimlik doğrulama yapar, yetkilendirme sağlamaz.

## OpenID Connect (OIDC)

* OAuth 2.0 üzerine inşa edilmiş modern bir kimlik doğrulama katmanıdır.  

* OAuth 2.0’ın yetkilendirme yeteneklerine kimlik doğrulama ekler.  

* ID Token kavramını getirir.  

* Günümüzde Google Sign-In, Microsoft Sign-In gibi servislerde kullanılan standarttır.

## OpenIddict

* ASP.NET Core projelerinde OAuth 2.0 ve OpenID Connect standartlarını kolayca uygulamak için kullanılan açık kaynak bir kütüphanedir.  

* Kendi kimlik/yetki sunucunuzu oluşturmanıza ve modern standartları uygulamanıza olanak tanır.

## Aralarındaki İlişki

| Teknoloji          | Amacı                     | Yetkilendirme | Kimlik Doğrulama |
|-------------------|--------------------------|---------------|-----------------|
| **OAuth (1.0)**   | Erişim izni sağlama       | Evet          | Hayır           |
| **OAuth 2.0**     | Modern yetkilendirme      | Evet          | Hayır           |
| **OpenID**        | Kimlik doğrulama (eski)   | Hayır         | Evet            |
| **OpenID Connect**| OAuth 2.0 + Kimlik doğrulama | Evet        | Evet            |
| **OpenIddict**    | Bu standartları .NET’te kolayca uygulama | Evet | Evet         |

</details>

<details> 

<summary>Performans artımı için ne yapılabilir? (AsNoTracking, IAsyncEnumerable, caching, profiling, redis)</summary>

1. * AsNoTracking:
     
Okuma amaçlı sorgularda AsNoTracking() kullanmak EF Core’un ChangeTracker özelliğini devre dışı bırakır. Bu sayede gereksiz bellek ve işlem yükü azalır. Özellikle listeleme veya raporlama gibi senaryolarda belirgin performans kazandırır.

2. * IAsyncEnumerable:
     
Büyük veri setlerini tek seferde belleğe almak yerine IAsyncEnumerable ile satır satır (streaming) çekmek hem bellek kullanımını hem de ilk yanıt süresini düşürür. Bu yöntem, sürekli veri işlenen veya çok büyük sorgularda işe yarar.

3. * Caching (Önbellekleme):
     
Sık kullanılan verileri tekrar tekrar veritabanından çekmek yerine önbellekte tutmak en etkili yöntemlerden biridir. Küçük uygulamalarda In-Memory Cache, ölçekli yapılarda ise Redis gibi Distributed Cache tercih edilmelidir.

4. * Profiling (İzleme):
     
Performansı artırmanın ilk adımı darboğazları tespit etmektir. MiniProfiler, EF Core logları veya SQL Profiler ile yavaş sorguları ve gereksiz işlemleri görebilir, buna göre optimize edebilirsiniz.

5. * Redis Kullanımı:
     
Dağıtık ve yüksek trafikli uygulamalarda Redis gibi bir önbellek sistemiyle veriyi sunucu dışında tutmak, veritabanı yükünü ciddi ölçüde azaltır ve yanıt sürelerini iyileştirir.

</details>

<details>

<summary>Performans için önerilen en az 3 teknik ve açıklamaları</summary>
 
| Teknik         | Amacı                                               | Kullanım Alanı            | Avantajı                     |
|----------------|----------------------------------------------------|--------------------------|------------------------------|
| AsNoTracking   | EF Core’da verilerin takip edilmesini devre dışı bırakmak | Sadece okuma sorguları   | Daha az bellek kullanımı, hızlı sorgu |
| Caching        | Sık kullanılan verileri önbellekte tutmak          | Tekrarlanan veri sorguları | DB yükünü azaltır, yanıt süresini kısaltır |
| Profiling      | Yavaş sorguları ve darboğazları tespit etmek       | Tüm uygulama             | Optimizasyon fırsatları sağlar |

</details>

## 7. Logging ve Hata Yönetimi

<details>
 
<summary>Neden loglama yapılır? Log seviyesi nedir?</summary>

1. Neden Loglama Yapılır?

   * Loglama, uygulamanın çalışması sırasında gerçekleşen olayların kaydedilmesidir. Amaçları:

   * Hata takibi: Uygulamada oluşan hataların kaynağını tespit etmek.
 
   * Performans izleme: Uzun süren işlemleri veya darboğazları belirlemek.

   * Olay kayıtları: Kullanıcı aktiviteleri veya sistem olaylarını kayıt altına almak.

   * Debug ve geliştirme: Kodun doğru çalışıp çalışmadığını anlamak.

   * Kısaca, loglama uygulamanın “görünmeyen gözlüğü” gibidir.

2. Log Seviyesi Nedir?


| Log Seviyesi    | Açıklama                                                                                   |
|-----------------|--------------------------------------------------------------------------------------------|
| Trace           | Çok detaylı bilgi; genellikle hataları veya işlemleri adım adım izlemek için kullanılır    |
| Debug           | Geliştiriciye yönelik bilgi; hata ayıklama ve sistem durumunu anlamak için kullanılır      |
| Information     | Normal çalışma bilgileri; kullanıcı aktiviteleri ve sistem olayları kaydedilir             |
| Warning         | Potansiyel sorunlar veya ileride hataya yol açabilecek durumlar hakkında uyarı verir       |
| Error           | Hata oluştu; işlem tamamlanamadı veya beklenmedik bir durum meydana geldi                  |
| Critical        | Kritik hata; sistemin çalışmasını veya uygulamanın devamını etkileyen durumları gösterir   |


  * Log seviyesi, uygulamadaki logların önem derecesini belirler ve hangi bilgilerin kaydedileceğini kontrol etmeye yarar.
    
</details>

<details>

<summary>ASP.NET Core'da logging altyapısı</summary>

* ASP.NET Core’da logging, uygulamanın çalışmasını takip etmek ve hataları kaydetmek için kullanılır. Temel yapı ILogger<T> ile sağlanır ve seviyeleri şunlardır: Trace, Debug, Information, Warning, Error, Critical.

Kullanım örneği:

```
public class HomeController : Controller
{
    private readonly ILogger<HomeController> _logger;
    public HomeController(ILogger<HomeController> logger) => _logger = logger;

    public IActionResult Index()
    {
        _logger.LogInformation("Index açıldı");
        _logger.LogWarning("Uyarı!");
        _logger.LogError("Hata!");
        return View();
    }
}
```

Program.cs’de yapılandırma:

```
var builder = WebApplication.CreateBuilder(args);
builder.Logging.ClearProviders();
builder.Logging.AddConsole();
builder.Logging.AddDebug();
builder.Logging.SetMinimumLevel(LogLevel.Information);

var app = builder.Build();
app.Run();
```

* Log sağlayıcıları: Console, Debug, EventLog, dosya (Serilog/NLog). 

</details>

<details>

<summary>Global exception handling nasıl yapılır?</summary>

ASP.NET Core’da global exception handling, tüm hataları tek bir noktada yakalamak için kullanılır. İki yaygın yöntem:

1. * Middleware ile
```
public class GlobalExceptionMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<GlobalExceptionMiddleware> _logger;

    public GlobalExceptionMiddleware(RequestDelegate next, ILogger<GlobalExceptionMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task Invoke(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Hata yakalandı.");
            context.Response.StatusCode = 500;
            context.Response.ContentType = "application/json";
            await context.Response.WriteAsync("{\"message\":\"Bir hata oluştu.\"}");
        }
    }
}

// Program.cs
app.UseMiddleware<GlobalExceptionMiddleware>();
```

2. * UseExceptionHandler ile

 ```  
app.UseExceptionHandler(app =>
{
    app.Run(async context =>
    {
        context.Response.StatusCode = 500;
        context.Response.ContentType = "application/json";
        await context.Response.WriteAsync("{\"message\":\"Bir hata oluştu.\"}");
    });
});
```

Özet: Tek noktadan hataları yakalayabilir, loglayabilir ve kullanıcıya mesaj gösterebilirsiniz.

 
</details>

<details>

<summary>UseExceptionHandler ve ILogger nasıl kullanılır?</summary>

```
Global Hata Yakalama
app.UseExceptionHandler("/error");
```

* Ne işe yarar: Yakalanmamış tüm hataları tek bir endpoint’e yönlendirir.

Hata Loglama
```
[Route("error")]
public IActionResult Error([FromServices] ILogger<ErrorController> logger)
{
    var ex = HttpContext.Features.Get<IExceptionHandlerFeature>()?.Error;
    if (ex != null) logger.LogError(ex, "Hata oluştu");
    return Problem(statusCode: 500);
}
```

* Ne işe yarar: Yakalanan hatayı loglar ve kullanıcıya güvenli bir hata mesajı döner.

Tek Middleware ile
```
app.UseExceptionHandler(a => a.Run(async ctx =>
{
    var logger = ctx.RequestServices.GetRequiredService<ILogger<Program>>();
    var ex = ctx.Features.Get<IExceptionHandlerFeature>()?.Error;
    if (ex != null) logger.LogError(ex, "Global hata");
    ctx.Response.StatusCode = 500;
    await ctx.Response.WriteAsync("Hata oluştu");
}));
```

* Ne işe yarar: Hem hatayı loglar hem de kullanıcıya basit bir mesaj gösterir, tek noktadan yönetim sağlar.
 
</details>

## 8. Yazılım Geliştirme Prensipleri

<details>

<summary>SOLID prensipleri: Her biri için kısa açıklama ve örnek</summary>

1. Single Responsibility Principle (Tek Sorumluluk Prensibi)
Bir sınıfın tek bir sorumluluğu olmalı.

```
// Kötü örnek
class UserService
{
    public void AddUser(string name) { /* kullanıcı ekle */ }
    public void SendEmail(string email) { /* mail gönder */ } // farklı sorumluluk
}

// İyi örnek
class UserService
{
    public void AddUser(string name) { /* kullanıcı ekle */ }
}

class EmailService
{
    public void SendEmail(string email) { /* mail gönder */ }
}
```

2. Open/Closed Principle (Açık/Kapalı Prensibi)
Sınıflar geliştirmeye açık, değişikliğe kapalı olmalı.

```
abstract class Discount
{
    public abstract double GetDiscount(double price);
}

class StudentDiscount : Discount
{
    public override double GetDiscount(double price) => price * 0.9;
}

class VIPDiscount : Discount
{
    public override double GetDiscount(double price) => price * 0.8;
}
```

3. Liskov Substitution Principle (Liskov Yerine Geçme Prensibi)
Türetilmiş sınıflar, temel sınıfların yerine sorunsuzca kullanılabilmeli.

```
class Bird
{
    public virtual void Fly() { }
}

class Sparrow : Bird
{
    public override void Fly() { /* uçar */ }
}

void MakeBirdFly(Bird bird)
{
    bird.Fly();
}
```

4. Interface Segregation Principle (Arayüz Ayrımı Prensibi)
Büyük bir arayüz yerine küçük ve amaca uygun arayüzler oluşturulmalı.

```
interface IWorkable
{
    void Work();
}

interface IEatable
{
    void Eat();
}

class Robot : IWorkable
{
    public void Work() { /* çalışır */ }
}
```

5. Dependency Inversion Principle (Bağımlılığı Tersine Çevirme Prensibi)
Yüksek seviye modüller, düşük seviye modüllere değil, soyutlamalara bağımlı olmalı.

```
interface ILogger
{
    void Log(string message);
}

class FileLogger : ILogger
{
    public void Log(string message) { /* dosyaya logla */ }
}

class App
{
    private readonly ILogger _logger;
    public App(ILogger logger) { _logger = logger; }
    public void Run() => _logger.Log("Çalışıyor");
}
```
</details>

<details>

<summary>Design Patterns: Singleton, Repository, Factory</summary>

1. Singleton Pattern

Amaç:
Bir sınıfın sadece tek bir örneğinin (instance) oluşturulmasını sağlar ve bu örneğe global erişim noktası sunar.

Örnek (C#):
```
public class Logger
{
    private static Logger _instance;
    private Logger() { }

    public static Logger Instance
    {
        get
        {
            if (_instance == null)
                _instance = new Logger();
            return _instance;
        }
    }

    public void Log(string message)
    {
        Console.WriteLine(message);
    }
}
```

Kullanım:
```
Logger.Instance.Log("Mesaj yazıldı");
```

2. Repository Pattern

Amaç:
Veri erişim işlemlerini (CRUD) soyutlayarak, uygulamanın veri katmanını iş katmanından ayırır.

Örnek (C#):
```
public interface IProductRepository
{
    IEnumerable<Product> GetAll();
    Product GetById(int id);
    void Add(Product product);
}

public class ProductRepository : IProductRepository
{
    private readonly AppDbContext _context;
    public ProductRepository(AppDbContext context)
    {
        _context = context;
    }

    public IEnumerable<Product> GetAll() => _context.Products.ToList();
    public Product GetById(int id) => _context.Products.Find(id);
    public void Add(Product product) => _context.Products.Add(product);
}
```

Kullanım:
```
var repo = new ProductRepository(context);
var allProducts = repo.GetAll();
```
3. Factory Pattern

Amaç:
Nesne oluşturma sürecini merkezileştirerek, istemci kodun hangi sınıfı oluşturacağını bilmesini önler.

Örnek (C#):
```
public abstract class Shape
{
    public abstract void Draw();
}

public class Circle : Shape
{
    public override void Draw() => Console.WriteLine("Daire çizildi");
}

public class Rectangle : Shape
{
    public override void Draw() => Console.WriteLine("Dikdörtgen çizildi");
}

public class ShapeFactory
{
    public static Shape CreateShape(string type)
    {
        return type switch
        {
            "Circle" => new Circle(),
            "Rectangle" => new Rectangle(),
            _ => throw new ArgumentException("Geçersiz şekil türü")
        };
    }
}
```

Kullanım:
```
Shape shape = ShapeFactory.CreateShape("Circle");
shape.Draw();
```

---


* Desing Patern Açıklaması

1. Singleton Pattern (Tekil Nesne Deseni)

Amaç: Bir sınıfın sadece bir nesnesi olmasını ve bu nesneye global erişim noktası sağlamayı garanti eder.

Kullanım: Konfigürasyon yöneticileri, loglama servisleri, veri tabanı bağlantıları.

Örnek (C#):
```
public class Singleton
{
    private static Singleton _instance;
    private Singleton() { }
    public static Singleton Instance
    {
        get
        {
            if (_instance == null)
                _instance = new Singleton();
            return _instance;
        }
    }
}

```
2. Factory Pattern (Fabrika Deseni)

Amaç: Nesne oluşturma sürecini bir fabrikaya bırakarak, nesne türünü çalışma zamanında belirlemeyi sağlar.

Kullanım: Nesne türleri değişken olduğunda, istemcinin nesne oluşturmayı bilmesine gerek yoktur.

Örnek (C#):
```
public interface IShape
{
    void Draw();
}

public class Circle : IShape
{
    public void Draw() => Console.WriteLine("Circle çizildi");
}

public class Square : IShape
{
    public void Draw() => Console.WriteLine("Square çizildi");
}

public class ShapeFactory
{
    public IShape GetShape(string shapeType)
    {
        return shapeType.ToLower() switch
        {
            "circle" => new Circle(),
            "square" => new Square(),
            _ => null
        };
    }
}
```
 
</details>

<details>

<summary>Clean Code nedir, neden önemlidir?</summary>
<br>

* Clean Code nedir

Clean Code kavramı, Türkçeye “Temiz Kod” olarak çevrilir. Robert Cecil Martin'in "Clean Code: A Handbook of Agile Software Craftsmanship" adlı kitabında bu kavramdan ve kavramın ilkelerinden bahsedilmiştir. Bu kitaptan sonra da Clean Code kavramı yaygınlaşmıştır. Ancak, Clean Code kavramı ve ilkeleri çok daha eskidir.

Sanıldığının aksine Clean Code, katı kurallar içermez. Clean Code; insanlar tarafından anlaşılması ve değiştirilmesi kolay kod yazmayı ifade eden bir dizi ilkedir. 

Anlaşılabilir kod yazmak, ister kodun geliştiricisi isterse de başka bir geliştirici tarafından kolayca anlaşılabileceği anlamına gelir. Tüm kodlar mantıklı ve açık bir şekilde yazılır. Kodun farklı bölümleri arasındaki bağlantılar açık ve nettir. Herhangi bir geliştirici tarafından her işlevin, yöntemin ve değişkenin görevi kolayca anlaşılır. 

* Clean Code neden Önemlidir

Geliştiriciler tarafından anlaşılmayan kodlar zamanla maliyeti artırır. Clean Code yazmak, maliyet tasarrufu sağlar.

Geliştiriciler kodu anlamaya çalışırken zamanının çoğunu burada harcar. Bu zaman gelecekteki her görevinden gereğinden fazla saatler alır. Clean Code; güvenilirliği, esnekliği ve performansı artırır. Bu nedenle de yazılım dünyasında önemli olan bir konudur.

Geliştiriciler kodu yazmaktan çok okumakta zaman harcarlar. Geliştiriciler genellikle mevcut kodu anlamaya çalışmak yerine kodu yeniden yazmayı tercih eder. Bu da zaman kaybına neden olur. İyi yazılmış, test edilmiş ve anlaşılır kod yazmak için zaman ayıran bir geliştirici, ilerleyen zamanda sayısız saat, gün ve hafta kazanır. Bu nedenle, her geliştirici kodla ilgilenmeli ve mümkün olduğunca kodu anlaşılır hale getirmelidir. 

</details>

<details>

<summary>Layered, Clean Architecture, Microservices, Event-Driven, Hexagonal Architecture (Ports & Adapters)</summary>
<br>

Layered Architecture:

* Kurulumu ve öğrenmesi kolay.

* Genellikle tek veritabanı ve tek deploy hattı olan küçük/orta ölçekli projelerde tercih edilir.

* Katmanlar: Presentation, Business, Data Access.

* Hiyerarşik yapı sunar.

Clean Architecture:

* Karmaşık iş kuralları ve değişken gereksinimler için uygundur.

* Domain merkezde tutulur, dış bağımlılıklar port–adaptör mantığıyla ayrılır.

* Test edilebilirlik ve bağımlılık yönetimi ön plandadır.

 Hexagonal Architecture (Ports & Adapters):

* Birden çok girdi/çıktı kanalı olan projelerde kullanılır.

* Domain ile dış dünya arasına port’lar ve adaptör’lar konur.

* Gevşek bağlılık sağlar.

Mikroservisler:

* Tek bir iş alanına odaklanan, bağımsız deploy edilebilen küçük servislerdir.

* Kendi veritabanlarına sahiptirler.

* Diğer servislerle API veya mesajlaşma ile haberleşir.

* Karmaşık ve ölçeklenebilir sistemlerde ekiplerin bağımsız çalışmasını sağlar.

 Event-Driven Architecture:

* Servisler arası iletişim olaylar (events) üzerinden yürür.

* Asenkron çalışır, loose coupling ile bağımlılık azaltılır.

* Bir olay gerçekleştiğinde diğer servisler bunu dinleyerek tetiklenir.

</details>
