\---- GRUP ----

2023280154  Melih Takyacı     melih.takyaci@ogr.deu.edu.tr
2023280140  Emre Özdemir      emre.ozdemir23@ogr.deu.edu.tr
2022280128  Eren Önder        eren.onder@ogr.deu.edu.tr
2022280050  Emir Mutlu        emir.mutlu@ogr.deu.edu.tr

\---- AÇIKLAMA ----

Kullandığımız kaynaklar:

* Pintos dokümantasyonu
* Stack Overflow tartışmaları
* AI toolari
* Online ders notları ve bloglar (örn. Pintos Argument Passing Explained, Pintos System Calls Deep Dive)


* https://github.com/st2092/pintos-user-programs/blob/master/README.txt
* https://github.com/wooseokyourself/pintos_project-2
* https://github.com/kevink1113/SGCS_CSE4070


  ```
           ARGÜMAN VERME
           ==============
  ```

\---- VERİ YAPILARI ----

A1:

* Fonksiyon imzaları değiştirildi:
  static bool setup\_stack(void \*\*esp, char \*\*saveptr, const char \*filename);
  bool load(const char \*file\_name, void (\*\*eip)(void), void \*\*esp, char \*\*saveptr);
  Amaç: Argümanları ayrıştırmak için filename ve saveptr parametreleri eklendi.

\---- ALGORİTMALAR ----

A2:

* setup\_stack fonksiyonunda argümanlar işlendi.
* char \*\*cont ve char \*\*argv dizileri tanımlandı.
* strtok\_r ile komut satırı ayrıştırıldı.
* Argümanlar ters sırada argv’ye kopyalandı ve yığına basıldı.
* Word alignment (4 byte) sağlandı.
* argv, argc ve sahte dönüş adresi yığına basıldı.
* Bellek temizlendi.

\---- GEREKÇE ----

A3:
strtok() global değişken kullanır, bu da çoklu iş parçacıklarında yarış durumuna neden olur. strtok\_r() reentrant olduğu için tercih edilmiştir.

A4:

1. Çekirdek argüman ayrıştırmak zorunda kalmaz, sadece çalıştırır.
2. Hata kontrolü ve kullanıcı geri bildirimi shell seviyesinde yapılır, sistem yükü azalır.

   ```
           SİSTEM ÇAĞRILARI
           ============
   ```

\---- VERİ YAPILARI ----

B1:

* thread:
  struct list file\_list → Açık dosyaları takip eder.
  int fd → Geçerli dosya tanımlayıcı.
  struct list child\_list → Çocuk süreçler listesi.
  tid\_t parent → Ebeveyn thread ID.
  struct child\_process \*cp → Çalışan çocuk süreç.
  struct file \*executable → Çalışan dosya.
  struct list lock\_list → Thread’in tuttuğu kilitler.
* syscall.h:
  struct child\_process → Çocuk sürecin PID, durum, semafor bilgileri.
  struct process\_file → Açık dosya, fd ve liste elemanı.
  struct lock file\_system\_lock → Dosya sistemi kritik bölümünü kilitler.

B2:
Dosya tanımlayıcıları her işlem içinde benzersizdir. Her işlem kendi fd sayacını tutar.

\---- ALGORİTMALAR ----

B3:
Kullanıcıdan gelen işaretçi doğrulanır. Sistem çağrısı numarası ve argümanlar kullanıcı yığından çekilir. Buffer geçerliyse okuma/yazma yapılır.

B4:
4096 bayt: en az 1, en fazla her sayfa başına bir get\_page çağrısı (örn. segment bölünmesine göre artar). 2 bayt: genelde 1. Daha az sayıda erişimle optimize edilebilir.

B5:
wait çağrısı process\_wait fonksiyonunu tetikler, child\_process’in çıkış durumunu bekler. Çıkış sinyali alınınca sentinel kontrolü yapılır.

B6:
Fonksiyon parçalama ile hata kontrolü ayrı tutulur. Sayfa hatası (page fault) interrupt handler’ı exit(-1) çağırır. Kilitler ve kaynaklar hata durumunda serbest bırakılır.

\---- SENKRONİZASYON ----

B7:
child\_process struct ile yüklenme durumu izlenir. Eğer yüklenmezse exec çağrısı -1 döner.

B8:
P, C’yi beklediğinde kilitler ve sentinel değişkenler kullanılır. P beklemeden çıkarsa çocuklar sonlandırılır ve kilitler serbest kalır.

\---- GEREKÇE ----

B9:
Sayfa hatası interrupt handler’ı ile bellek erişimi yönetildi. Bu, hata kontrolünü merkezi hale getirir.

B10:
Her işlem için benzersiz fd sayacı yarış durumu riskini azaltır.

B11:
Varsayılan tid\_t → pid\_t eşlemesi kullanıldı, ek karmaşıklık eklenmedi.

\---- ANKET ----


İşletim sistemi çekirdeğinin nasıl çalıştığını ve kullanıcı programlarıyla nasıl etkileşime girdiğini anlama.

Sistem çağrılarının nasıl tasarlandığını, senkronizasyon ve kaynak paylaşımı problemlerini çözmeyi öğrenme.

Bellek yönetimi, yığın düzeni ve bellek erişim güvenliğini uygulamalı öğrenme.

Çekirdek–kullanıcı alanı ayrımı ve güvenlik konularında derinleşme.

Çok iş parçacıklı (multithreaded) ortamda yarış durumu ve deadlock problemlerini çözme.

Büyük, karmaşık bir kod tabanında nasıl ilerlenir ve tasarım kararları nasıl verilir, bunu deneyimleme.

Bu proje bana özellikle sistem çağrılarında hata yönetimi ve senkronizasyonun ne kadar hassas olduğunu, bu tür bir yazılımda basit bir hatanın bile tüm sistemi etkileyebileceğini öğretti.

\--- SON ---
