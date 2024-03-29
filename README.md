# Assembly-Notlarim
2021 Yılıından itibaren assembly dili ile 8051 ve Aduc 841 Derslerimden Notlar

Ders 10:
----------------
Piyasaya argeci olarak çıkılırsa en az 100 farklı marka mikroişlemci ile karşılaşılacak (8051, ARM, Intel, Atmel, PIC, TI)
Temel üzerine anlatılan bu ders iyi öğrenildiği taktirde diğer mikroişlemcilere geçil yapıldığında hızlı şekilde çözümleme yapılabiliyor.
Amaç 8051 öğrenmek değil, mikroişlemci temellerini öğrenmektir, bir ta nesini iyi öğrendikten sonra diğerkerini hızlı bir şekilde öğrenilebilir..

8051 standartı: 
5 çevre birimi:

I/O
RAM
Flash
Timer
Seriport

Farklı bir firma geliyor ve "Ben sana patentin parasını veriyorum ve kendi mikroişlemcimi üreteceğim" diyor ve ADUC841 isimli gelişmiş bir 
mikroişlemci sunuyor ve ADUC841, standart 8051'deki çevre birimlerini genişletiyor. (Regidsterlar türetiyor ve bu registerlar ile donanımı haberleştiriyor.)

Flash Hafıza (EEPROM): Elektrik kesilse bile içindeki bilgi kaybı olmaz.

Gerçek hayatta bir çip aldığımızda onu programlayıcı bir cihaza ihtiyacımız var.

P0 ve p2, P1 ve P3 birbirine benziyor, P0 ve P2 pull up yok. P1 ve P3 pull up var. Yani multimetre ile pinden 5V değer alıyoruz.

Bazı mikroişlemcilerde pin yönlendirmek vardır(input veya output ayarlamak) fakat 8051 yapısında I/O ayarlamamıza
gerek yok.

setb P1.0 dediğimiz zaman verdiğimiz 1 direkt çıkışa gitmiyor. Flip flop pull up ileç ıkan 5V değeri çıkışı 1 yapıyor. (Notlar sayfa 98 Pull up yapısı)
clr P1.0 dediğimiz zaman ise pull up yapısındaki toprak çıkışa kısa devre olup çıkışı 0 yapıyor. (Doalylı yoldan çıkış sıfırlanıyor)

P0 ve P2 ise harici input output olarak kullanılması haricinde harici Ram'e ile ilgili işlemler için yapılmış.(Not sayfa 91)
Portların pinlerin özellikleri (Notlar sayfa 105)

BÖLÜM 7 ZAMAQNLAYICI - SAYICI YAPISI
8051'de standart olarak Timer0 ve Timer 1 var. 8052 türevi mikroişklemciklerde Timer2 var.(Aduc841)

4Mhz -> Saniyede 4 milyon kare dalga var. (Her kare dalga 1/4000000 saniye)
Timer zamanı sayıyor. Ama burdaki zamandan kastedilen kare dalga saymaktır. 4 milyon kare dalga sayarsa 1 saniye geçti demektir.
Yani zaman saymak demek osilatörün her cycle'ını saymak demek oluyor. Yazılan kod CPU içerisinde koşuyor fakat sayma işlemini CPU yerine Timer denen elaman yapıyor.

Timer0 'ın yapabildiği başka bi şey pin sayabilmektir. (Pinden gelen düşen kenarları hem de zamanı sayabiliyor)
Aduc841'de osilatör olarak 110592 kullanılır bu bir özel değerdir. 1 kare dalga süresi 90,4ns ile 11.0592 çarpılırsa 1 saniye elde edilir.

Farklı bir yol: 1 saniye saymak için 11059200 tane kare dalga sayılırsa 1 saniye geçer.

Timer0 hem 9 bitlik hem 16 bitlik registerı var(8+8 bölünmüş) 65535'e kadar sayıyor. 2^16 - 1 değerine eşit.
TH0 + TL0 = 16 bit

Timer0 ya osilatör sayıyor ya pin sayıyor (Düşen kenarları sayıyor) (Notlar sayfa 108)
P3.4 -> Timer0
P3.5 -> Timer1

--------------------------------------
Timer kod blok örnek ->

org 00h
ayar:

//Timer ayarları, Ayarlar 1 kez yapılır kodda sürekli dönmez.

Basla: 

-------------------------------------

1. Başlangıç değeri ne? (Aşağı veya yukarı olabilir) Bizde yukarı sayıyor.

100 tane saymak istiyorsak 65435'ten başlatıp 65535'te bayrağı taşıracağız.

-------------------------------------
Örnek Ayar:

    org 00h
    sjmp ayar

ayar:
    mov TMOD, #00000101B -> sondaki 2 tanesi MOD1 (16 bitlik sayıcı olup olmadığını ayarlıyor) Sonraki 1 değeri counter olduğunu ayarladı.
                                                  (Sonraki 0 değeri gate, Dışardan başlatma olayı)
                                                  (65435 değerini TL0 ve TH0 registerlarına kaydedilecek)
    mov TL0,#9Bh
    mov TH0,#0FFh
                  (65435 değerine ayarlamış olduk)
    setb TR0
                  (Zamanlayıcı başladı)
x: 

    jnb TF0,x
    clr TF0,
    mov TL0, #9Bh
    mov TH0, #0FFh
    cpl P1.0 (Görev)
    
    sjmp x


DERS 11: TIMER COUNTER T/C
----------------------------------------------------------------
100 tane paket sayalım,
----------------
org     ooh
sjmp    ayar

ayar:  mov TMOD, #xxxx0101B (gate- counter ve mod 1 ayarlandı)
//65535 - 100
DPTR= DPH + DPL
65435d değerini direkt DPTR verirsek DPH ve DPL atama yapıyor
mov DPTR, #65435d
mov TL0, DPL
mov TH0, DPH
setb TR0   
x: jnb TF0, x
clr TF0
mov TL0, DPL
mov TH0, DPH
clr TF0(dersek sayıcı durur)
cpl P1.0 (Görev)

sjmp x


5 ms'de 1 ledin cpl alalım ->
-----------------
osc = 110592 Mhz ise 

1 saniye geçmesi için sayılması gereken osilator miktarı 11059200 adet
5 milisaniye için 55296 adet say

sjmp ayar
ayar: 
mov TMOD, #0001xxxxB
mov DPTR, #10239d
mov TL1, DPL
mov TH1, DPH
setb TR1
x:
jnb  TF1, x
clr TF1
mov TL1, DPL
mov TH1, DPH
cpl P1.0
sjmp x

----------------------------
Mod1 Sayıcı
---
Gate 1 ise sayıcı dışarıdan başlatılır.
setb TR0 dersen bile sayıcı çalışmaz(Gate 1 olduğu için)
(Notlar sayfa 109)

Kesmeler
---
Kimler kesme üretebilir?
Timerlar, ADC, Seri port, I/O

Harici kesme 0 gelmişse ->      0003h
Timer 0 kesemsi gelmişse ->     000Bh
Harici kesme 1 gelmişse ->      0012h
Timer 1 kesmesi gelmişse ->     001Bh
                                        adreslerine gidiyor.

Harici kesme 0 için
---
org 00h
smjp    ayar
        (Harici 0 kesmesi düşen kenar)
setb    it0 (Düşen kenar kesmesi aktif)
setb    ex0 (Harici kesme aktif) (et0 deseydim timmer kesmesi aktif olacaktı)
setb    ea (Tüm kesmeler aktif)

--------
Hafta 12 ADC
---
Analog - Digital

Bir derece var, biz bu dereceyi sensör ile gerilime dönüştürüyoruz. O gerilimden gelen çıktıları
    bilgisayara girdik. Bunlar da 0-4095 arasındaki sayılara dönüşüyor.

Analog veriyi mikroişlemcinin içine sayı olarak almak istiyoruz, o yüzden ADC bağlıyoruz ve
    bu ADC'nin bir referans gerilimi var.(Vref)

Kaç bitlik ADC?
Aduc841 12 bitlik bir ADC (Girilen verileri 12bit olarak geri veriyor).
    Maksimumda 2^12-1 değerini verecek.

100 Derecede -> 5V      -> 1111 1111 1111 Değeri verecek (4095)
50  Derecede -> 2,5V    -> 2048
0 Derecede   -> 0V      -> 0

Vref ne demek?
---
-> bana Vref gelirse ben 4095 üretirim.(12 bit için)

Çözünürlük -> 5/4095 (ADC/Bit sayısı)
Bit değeri arttıkça hassaslık artacak.
ADC değerleri kesinlikle hatalıdır. Fakat bu hata payını ADC bit'ine göre azaltabilir.
    8 bitlik bir ADC kullanırsak hata çok daha fazla büyüyecek, genel olarak 12 bit ADC yeterlidir.
ADC içinde bir mux yapısı var, Seçiciler ile girişlerdeki hangi pini ölçeceğini belirliyor(2 giriş aynı anda okunamıyor)
ADC dahili referansı var (2,5V), istersen bunu kullanırsın istersen de harici bir Vref bağlarsın.

ADC 4 türlü çalışıyor ->
---
    ADC'yi direkt çalıştırabilirsin. Bitiyor bir daha çalışmıyor sen tekrar başlatıyorsun. (setb sconv)
    ADC yi durmadan çalıştırabilişrsin  (setb cconv) Continious conversion
    Dışardan başlatmak. (External start)    Buton bağlanır ve buton ile başlatılır.
    Timer 2 ile başlatılır. 16 bitlik bir timerdır. 65535'ten sonra taşma olunca ADC otomatik başlıyor.

Kanal 1'den gelen örneği okuyalım
---

    #include "aduc841.h"
    sjmp    ayar

ayar: 

    mov     addcon1, #10111100b // mdi dahili ref
    mov     addcon2, #00000001b // 1. kanal seçildi
y:  setb    sconv   //adc calisti
                    //bekle cevrim bitmesi
                    //adci=1 oldun mu
x:  jnb     adci,x
    clr     adci    //bayragi temizle
    mov     a,#0Fh
    anl     a,ADCDATAH
    mov     r0,a
    mov     r1,ADCDATAL
    sjmp    x
    
    end
    
Hafta 14:   DAC
---

2 Adet DAC'ımız var. DAC0, DAC1. Aynı anda 2 analog çıkış üretebiliriz. (0 ile referans gerilimi arasında)

DACCON register -> Notlar sayfa 138 bak.

SYNC = 1 ise direkt çıkış oluşur.
SYNC = 0 ise DAC'lar çalışmıyor.

Aynı anda nasıl çıkış alırız? 
    DAC'ların low'larına veri yazıldığı anda çıkış başlar. (İlk önce high lar yazılır)
    İkisii aynı andan çalıştırmak istiyorsan SYNC=0 yap, verileri yaz sonra SYNC=1 yaz.
    
Hafta 15: PWM
---
10 ms yakıyoruz 10 ms söndürüyoruz. O kadar hızlı oluyor ki göremiyoruz. 5V ve 0V -> bunu 2.5 volt olarak görüyoruz.
Maksimum 6000rpm motoru %50 ile 3000 rpm döndürürüz. Darbe genişliğini ayarlıyoruz.

Periyot ne?
Bunun ne kadarı Ton?

PWM bir timer'dır. Kesmesi yok. Osilatör sayıyor.
2 tane PWM var -> PWM0, PWM1 
PWM0H, PWM0L
PWM1H, PWM1L

### ALU (Aritmetic Logic Unit)


- ALU, Aritmetik ve Mantıksal Birim (Arithmetic Logic Unit) anlamına gelir. Bilgisayarın işlemci (CPU) bileşeninin bir parçasıdır. Temel aritmetik (toplama, çıkarma, çarpma, bölme) ve mantıksal (AND, OR, NOT, XOR vb.) işlemleri gerçekleştirmekten sorumludur.

- ALU'nun çıktısı genellikle bir akümülatöre yazılır. Akümülatör, işlemci içindeki bir tür kayıttır (register). ALU tarafından gerçekleştirilen aritmetik ve mantıksal işlemlerin sonuçlarını tutar. İşlemci, bu sonuçları daha sonraki işlemler için kullanabilir.

- Örneğin, bir program, 5 ve 3'ün toplanmasını talep ettiğinde, bu sayılar ALU'ya aktarılır. ALU, bu iki sayıyı toplar ve sonucu (8) akümülatöre yazar. Bu değer daha sonra program tarafından talep edildiğinde akümülatörden alınabilir. Bu, ALU ve akümülatör arasındaki temel bağlantıyı örneklemektedir.

- Ancak, modern mikroişlemcilerde ALU ve akümülatör arasındaki bağlantı biraz daha karmaşık hale gelmiştir. Mikroişlemciler genellikle çoklu ALU'lara ve genel amaçlı kayıtlara (akümülatörün daha genelleştirilmiş bir versiyonu) sahiptir ve bu kayıtlar, çeşitli işlemler için gerekli verileri ve sonuçları tutmak için kullanılır. Bu, mikroişlemcilerin karmaşık işlemleri daha hızlı bir şekilde gerçekleştirmesine olanak sağlar.

### PC (Program Counter)

- Program Sayacı (Program Counter veya PC), bilgisayarın merkezi işlem birimi (CPU) içindeki bir register'dir. PC, genellikle bir sonraki komutun bellekte (genellikle RAM'de) nerede bulunduğunu belirten bir adresi saklar. İşlemci bu adresi kullanarak bellekten ilgili komutu alır, işler ve ardından program sayacını bir sonraki komuta ilerletir.

- Program sayacının işleyiş şekli aşağıdaki gibidir:

1. Bilgisayarın başlatılması veya bir programın çalıştırılmasının başlaması ile program sayacı, programın ilk komutunun adresini saklar.

2. CPU, program sayacının gösterdiği adresteki komutu bellekten alır ve işler.

3. İşlem tamamlandığında, program sayacı genellikle bir sonraki komutun adresine otomatik olarak ilerler. Bu genellikle mevcut adresin bir veya birkaç birim artırılması anlamına gelir, çünkü çoğu program komutları sıralı olarak yerleştirir.

4. Ancak, bazı komutlar (örneğin, bir döngüyü başlatan veya bitiren komutlar, bir alt rutine atlayan komutlar veya koşullu bir ifadeye dayalı olarak farklı bir komuta atlayan komutlar) program sayacının değerini doğrudan değiştirebilir. Bu durumda, program sayacı belirtilen yeni adrese atlar ve CPU bu adresten yeni komutu alır.

5. Bu işlem, bir programın sonuna ulaşılıncaya veya bilgisayarın kapatılmasına kadar devam eder.

- Program sayacının işlevi, CPU'nun hangi komutu ne zaman işleyeceğini belirlemek olduğundan, program sayacı, bir bilgisayarın işlemlerini düzenli ve öngörülebilir bir şekilde sıralamasını sağlar.

### WR (working register)

- Working Register (WR), bir işlemcinin bir parçası olan ve genellikle işlemci tarafından gerçekleştirilen hesaplamalarda geçici veri depolama işlevi gören bir tür register'dir. İşlemcilerde farklı türdeki registerlar olabilir ve her biri belirli bir işlevi yerine getirir. Working register, genellikle ALU (Aritmetik ve Mantıksal Birim) tarafından işlenen veya işlemci tarafından erişilmesi gereken verileri saklar.

- Working register'ın işleyişi genel olarak aşağıdaki gibi olabilir:

1. Bir program, belirli bir hesaplama gerçekleştirmeyi talep eder. Örneğin, iki sayının toplanması talep edilebilir.

2. Bu sayılar, hesaplamanın gerçekleştirileceği working register'a yüklenir.

3. ALU, bu sayıları alır ve talep edilen işlemi (bu örnekte toplama) gerçekleştirir.

4. ALU'nun hesaplama sonucu, genellikle sonucun daha sonra kullanılabilmesi için bir register'a (örneğin, bir accumulator'a) geri yazılır.

5. Working register, aynı zamanda, hesaplama sonucunun bir sonraki işlemde kullanılmasını gerektiren durumlar için sonucu saklayabilir.

- Yukarıdaki adımlar, bir working register'ın genel işleyişini açıklar, ancak belirtmek önemlidir ki, belirli bir işlemcinin veya bilgisayarın tasarımına bağlı olarak bu işleyiş değişebilir. Bazı sistemlerde, working register aynı zamanda bir akümülatör işlevi de görebilir. Diğer sistemlerde, bir veya daha fazla working register, genel amaçlı registerlar olarak hizmet verebilir ve bir dizi farklı işlem için veri saklayabilir.

### Stack pointer

- Bilgisayar biliminde "stack" (Türkçe'de "yığın" olarak da ifade edilir), belirli bir veri tipinden öğelerin düzenli bir şekilde saklandığı bir veri yapısıdır. Stack, Last-In-First-Out (LIFO) prensibine göre çalışır, yani en son eklenen öğe ilk olarak çıkarılır.

- Stack'in işlevi biraz bir yığın tabağa veya kitaba benzetilebilir. Yeni bir tabak veya kitap eklediğinizde, bu genellikle yığının en üstüne gelir. Bir tabağı veya kitabı çıkarmak istediğinizde, genellikle en üstte olanı alırsınız.

- Bilgisayar sistemlerinde, stack genellikle iki ana işlev görür:

1. **Alt Rutinler/Fonksiyonlar:** Bir programın çeşitli fonksiyonları ve prosedürleri, çoğunlukla stack üzerinde çalışır. Bir fonksiyon çağrıldığında, çağrılan fonksiyonun bilgileri (parametreler, dönüş adresi vb.) stack üzerine "push" (yığına eklenir) edilir. Fonksiyon tamamlandığında, bu bilgiler stack'ten "pop" (yığından çıkarılır) edilir ve kontrol çağrıyı yapan koda geri döner.

2. **Geçici Depolama:** Stack, bir program tarafından kullanılan geçici verileri depolamak için de kullanılabilir. Bir program, bir değeri daha sonra kullanmak üzere stack üzerine ekleyebilir ve bu değeri ihtiyaç duyduğunda stack'ten çıkarabilir.

- Stack genellikle RAM'de (Rastgele Erişimli Bellek) bulunur ve işlemci tarafından kullanılır. İşlemcinin bir "stack pointer" adlı özel bir register'ı vardır, bu pointer stack'in en üstündeki öğenin konumunu gösterir. Bir öğe stack'e eklenirken veya stack'ten çıkarılırken, stack pointer buna uygun olarak güncellenir.

- Stack Pointer (SP), bir bilgisayarın merkezi işlem biriminin (CPU) bir parçası olan bir tür register'dir. Stack pointer, genellikle bilgisayarın belleğindeki bir stack (yığın) veri yapısının en üstündeki (veya bazen en altındaki) öğenin konumunu belirler.

- Stack, Last-In-First-Out (LIFO) prensibine göre çalışan bir veri yapısıdır. Yeni bir öğe eklediğinizde (push işlemi), bu en üste gelir ve bir öğeyi çıkardığınızda (pop işlemi) genellikle en üstteki öğeyi alırsınız.

- Stack pointer'ın işleyişi aşağıdaki gibi olabilir:

1. Stack pointer, başlangıçta stack'in en üst noktasını gösterir.

2. Bir öğe stack'e eklenmek istendiğinde (push), stack pointer, bellekteki yeni öğenin konumuna ilerler ve yeni öğe bu konuma yazılır.

3. Bir öğe stack'den çıkarıldığında (pop), stack pointer en son eklenen öğeyi gösterir ve bu öğeyi alır, ardından stack'in bir önceki öğesini göstermek üzere geri ilerler.

4. Stack pointer ayrıca, alt rutinler (fonksiyonlar veya prosedürler) ve kesmeler (interrupts) gibi işlemler sırasında program sayacının eski değerini saklamak için kullanılır. Bu, alt rutinin veya kesmenin tamamlanmasının ardından işlemcinin nereden devam edeceğini belirler.

- Bu adımlar, stack pointer'ın genel işleyişini tanımlar. Ancak, belirli bir işlemci veya bilgisayar sisteminin tasarımına bağlı olarak bu işleyiş biraz değişebilir. Örneğin, bazı sistemlerde stack yukarı doğru büyür (yani, yeni öğeler daha yüksek bellek adreslerine eklenir), diğerlerinde ise aşağı doğru büyür. Bu durum, stack pointer'ın yeni öğeler eklenirken veya mevcut öğeler çıkarılırken nasıl hareket edeceğini belirler.

### Alt programa girildiğinde stack Pointer ve Program counter?

- Bir alt programa (subroutine veya function) girildiğinde genellikle bazı veriler stack bölgesine push edilir. Bu veriler genellikle mevcut program durumunu korumak için gereklidir, böylece alt program tamamlandığında ana programa dönebiliriz ve kaldığımız yerden devam edebiliriz.

- Stack Pointer (SP) ve Program Counter (PC) bu süreçte önemli roller oynar:

1. Stack Pointer (SP): SP, stack'teki en üst konumu işaret eder. Bir alt programa girerken, genellikle mevcut işlem durumunu korumak için bazı bilgiler stack'e push edilir. Bu bilgiler genellikle mevcut programın durumunu korumak için gereklidir ve stack pointer, yeni verinin stack'in neresine yerleştirileceğini belirler.

2. Program Counter (PC): PC, bir alt programın başlangıç adresini içerir ve bu adres, PC'ye yüklenir. Sonrasında, işlemci alt programı çalıştırırken PC, sıradaki talimatın adresini saklar. Bir alt programa girerken genellikle PC'nin mevcut değeri stack'e push edilir. Bu, alt programdan döndüğümüzde, programın nerede kaldığını bilmemizi sağlar, çünkü PC'nin eski değerini stack'den pop edip PC'ye geri yükleriz.

- Hangi verilerin stack'e push edileceği, kullanılan dil ve alt programın kendine özgü ihtiyaçlarına bağlıdır. Ancak, genellikle aşağıdaki türden veriler stack'e push edilir:

1. **Çalışma zamanı değişkenler:** Bu değişkenler alt programın yerel değişkenleridir. Alt program tamamlandığında, bu değişkenler stack'den pop edilir ve yok olurlar.
2. **Return adresi:** Alt programdan döndüğümüzde devam etmek için kullanılan adres. Genellikle alt programa girerken mevcut PC'nin değeri alınır ve stack'e push edilir.
3. **Kaydedici durumları:** Alt programın çalışması sırasında değiştirilebilecek bazı işlemci register'ları. Bu register'ların durumları, alt programa girerken genellikle stack'e push edilir, böylece alt programdan döndüğümüzde bu durumları geri yükleyebiliriz.
- Bu şekilde, alt programlar ve stack mekanizması, programın farklı bölümlerini etkin bir şekilde yönetmeyi ve işlem durumlarını korumayı sağlar.

### Watchdog Timer

- Bir Watchdog Timer (WDT), bir mikroişlemci sistemini izleyen ve belirli bir süre boyunca herhangi bir etkinlik olmazsa, genellikle bir yazılım hata veya donma durumu nedeniyle, sistemi yeniden başlatan veya başka bir işlem gerçekleştiren bir tür donanım zamanlayıcıdır.

- Watchdog timer'ın çalışma prensibi genellikle şöyledir:

1. Yazılım, watchdog timer'ı belirli bir zaman aralığı ile başlatır (resetler).

2. Eğer belirlenen zaman aralığında yazılım tarafından timer resetlenmezse, bu durum genellikle yazılımda bir hata olduğu anlamına gelir (örneğin, bir süreç takılmıştır veya bir döngü sonsuz döngüye girmiştir).

3. Watchdog timer zaman aşımına uğradığında, bir "watchdog bite" (watchdog ısırığı) oluşur. Bu, genellikle sistemi yeniden başlatan veya belirli bir donanım parçasını sıfırlayan bir işlemi tetikler. Bu, sistemin tekrar düzgün çalışması için genellikle hatalı durumdan kurtulmasına yardımcı olur.

- Bu süreç, sürekli devam eder ve genellikle yazılımın düzenli aralıklarla watchdog timer'ı resetlemesi gerektirir. Bu genellikle belirli bir komut veya işlem kullanılarak yapılır.

- Watchdog timer'lar, özellikle gömülü sistemlerde yaygın olarak kullanılır, çünkü bu sistemler genellikle uzun süreler boyunca güvenilir bir şekilde çalışmalı ve potansiyel yazılım hatalarına karşı dayanıklı olmalıdır. Bununla birlikte, doğru kullanıldığında, watchdog timer'lar bir bilgisayar sisteminin genel güvenilirliğini ve dayanıklılığını büyük ölçüde artırabilir.



