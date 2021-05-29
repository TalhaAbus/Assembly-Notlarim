# Assembly-Notlarim
2021 Yılıından itibaren assembly dili ile 8051 ve Aduc 841 Derslerimden Notlar

Ders 10:

Piyasaya argeci olarak çıkılırsa en az 100 farklı marka mikroişlemci ile karşılaşılacak (8051, ARM, Intel, Atmel, PIC, TI)
Temel üzerine anlatılan bu ders iyi öğrenildiği taktirde diğer mikroişlemcilere geçil yapıldığında hızlı şekilde çözümleme yapılabiliyor.
Amaç 8051 öğrenmek değil, mikroişlemci temellerini öğrenmektir, bir ta nesini iyi öğrendikten sonra diğerkerini ızlı bir şekilde öğrenebiliriz.

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


















