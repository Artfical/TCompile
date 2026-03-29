# TCompile: T-Sharp İkili Dönüştürme ve Derleme Motoru

![Lisans](https://img.shields.io/badge/Lisans-GNU_AGPL_v3-red.svg)
![Sürüm](https://img.shields.io/badge/Sürüm-v4.1-blue.svg)
![Altyapı](https://img.shields.io/badge/Altyapı-PyInstaller_Core-yellow.svg)
![Sistem](https://img.shields.io/badge/Sistem-Windows_Linux_MacOS-lightgrey.svg)

---

## Proje Geliştiricisi ve Mimarı

TCompile modülünün tüm dönüştürme algoritmaları, AST (Soyut Sözdizimi Ağacı) yönetimi ve paketleme entegrasyonu tamamen bireysel bir mühendislik çalışmasıdır.

| Geliştirici | Katkı Oranı | Portfolyo |
| :--- | :---: | :--- |
| [**Talha Berk Arslan**](https://github.com/Codertalha5524) | %100 | Baş Geliştirici / Yazılım Mimarı |

---

## 1. TCompile Nedir ve Neden Gereklidir?

T-Sharp (T#) dili, kodlamayı Türkçe komutlarla öğretmeyi amaçlayan güçlü bir yorumlayıcıdır (interpreter). Ancak geliştirilen bir projenin (bir oyun, görsel arayüz veya ağ aracı) son kullanıcıya ulaşabilmesi için, kullanıcının bilgisayarında T-Sharp veya Python kurulu olma zorunluluğu ortadan kaldırılmalıdır. 

İşte TCompile bu noktada devreye girer. TCompile, kaynak kodu (.tsharp) okur, optimize edilmiş bir ara koda dönüştürür (Transpilation) ve ardından paketleme motorunu tetikleyerek projenin tüm bağımlılıklarıyla birlikte tek bir bağımsız çalıştırılabilir dosyaya (.exe veya ELF) dönüşmesini sağlar (Compilation).

---

## 2. Teknik Mimari ve Çalışma Prensibi

TCompile, doğrusal bir derleme hattı (pipeline) kullanır. Süreç üç ana fazdan oluşur:

### Faz 1: Kaynak Kod Analizi ve Dönüşüm (Transpilation)
TCompile, argüman olarak verilen .tsharp uzantılı dosyayı belleğe alır. Düzenli ifadeler ve TSharp çekirdek yorumlayıcısının mantıksal kurallarını kullanarak, Türkçe komutları standart ve hatasız bir Python koduna satır satır çevirir. Bu aşamada sözdizimi hataları (syntax error) yakalanırsa derleme işlemi durdurulur ve geliştiriciye bilgi verilir.

### Faz 2: Bağımlılık Haritalandırma
Oluşturulan ara kod analiz edilerek, projenin hangi kütüphaneleri kullandığı tespit edilir. Eğer projede "kullan gui" komutu varsa Tkinter, "kullan gui6" komutu varsa PySide6, "kullan oyun" komutu varsa Pygame bağımlılıkları derleme listesine otomatik olarak eklenir ve gereksiz kütüphanelerin paketi şişirmesi engellenir.

### Faz 3: İkili (Binary) Paketleme
Bu aşamada derleme motoru devreye girer. TCompile, arka plandaki motora gerekli parametreleri ileterek, işletim sistemine uygun yerel makine kodunu ve gerekli dinamik kütüphaneleri (DLL/SO) tek bir paketin içine sıkıştırır.

---

## 3. Kurulum ve Sistem Gereksinimleri

TCompile'ın stabil çalışabilmesi için geliştirici ortamında aşağıdaki bileşenlerin kurulu olması zorunludur:

* Python Yorumlayıcısı: Sürüm 3.12 veya üzeri.
* Derleyici Altyapısı: PyInstaller (pip install pyinstaller komutu ile yüklenmelidir).
* Gerekli Kütüphaneler: Projenizin içeriğine göre PySide6, Pygame, Requests, NumPy gibi kütüphanelerin ana sistemde kurulu olması gerekir.

---

## 4. Kullanım Kılavuzu ve Parametreler

TCompile, komut satırı (CLI) üzerinden yönetilen bir araçtır. Derleme işlemini başlatmak için sistem terminalini (CMD, PowerShell veya Bash) açıp TCompile'ın bulunduğu dizine gitmeniz gerekmektedir.

### Temel Kullanım (Konsol Uygulamaları)
Matematiksel hesaplamalar, dosya okuma/yazma veya ağ üzerinden veri çekme gibi sadece terminal ekranında çalışan projeler için varsayılan komut kullanılır:

python tcompile.py projem.tsharp

Bu komut, "projem.tsharp" dosyasını derler ve çıktıyı otomatik olarak oluşturulan "dist/" klasörü içerisine yerleştirir.

### Gelişmiş Derleme Parametreleri
TCompile, projenizin türüne göre derleyici argümanlarını yönetmenizi sağlayan çeşitli bayraklara (flags) sahiptir. Komutun sonuna aşağıdaki parametreleri ekleyebilirsiniz:

* --arayuz (Windowed Mod): Projeniz Tkinter veya PySide6 tabanlı bir görsel form içeriyorsa bu parametre kullanılmalıdır. Bu sayede program çalıştırıldığında arka planda siyah bir terminal/konsol penceresi açılmaz, sadece tasarladığınız görsel arayüz ekrana gelir.
  Kullanımı: python tcompile.py projem.tsharp --arayuz

* --tek-dosya (Onefile Mod): Varsayılan olarak derleyici bir klasör yapısı oluşturur (içinde uygulamanız ve bir sürü destekleyici kütüphane dosyası bulunur). Eğer yazılımınızı taşınması kolay tek bir .exe dosyası haline getirmek istiyorsanız bu parametreyi kullanmalısınız.
  Kullanımı: python tcompile.py projem.tsharp --tek-dosya

* --ikon <ikon_yolu>: Derlenen uygulamanıza özel bir simge atamak için kullanılır. Windows için .ico uzantılı bir dosya yolu belirtilmelidir.
  Kullanımı: python tcompile.py projem.tsharp --arayuz --ikon resimler/logo.ico

### Tam Kapsamlı Örnek
Görsel arayüze sahip, tek bir çalıştırılabilir dosya olacak ve özel bir ikona sahip bir projenin derleme komutu şu şekildedir:

python tcompile.py ana_program.tsharp --arayuz --tek-dosya --ikon varliklar/uygulama.ico

---

## 5. İleri Düzey Derleme Senaryoları

Büyük çaplı projelerde (örneğin devasa resim dosyaları ve müzikler içeren bir Pygame projesi) standart parametreler yeterli olmayabilir.

* Medya Dosyalarını Dahil Etme: Projenizdeki ses ve resim dosyalarını derlenmiş exe'nin içine gömmek isterseniz, TCompile'ın ürettiği ".spec" dosyası üzerinden "datas" dizisini düzenlemeniz gerekir.
* Gizli İçe Aktarmalar (Hidden Imports): T-Sharp'ın dinamik yapısı bazen kütüphanelerin doğrudan analiz edilmesini zorlaştırabilir. Bu durumlarda derleyiciye ilgili kütüphanenin adını manuel olarak belirterek pakete dahil edilmesini sağlayabilirsiniz.

---

## 6. Derleme Çıktıları ve Dizin Yapısı

Derleme işlemi başarıyla tamamlandığında, çalışma dizininizde iki yeni klasör ve bir konfigürasyon dosyası oluşacaktır:

* build/ Klasörü: Derleme sırasında kullanılan geçici önbellek ve log dosyalarını barındırır. İşlem bittikten sonra güvenle silinebilir.
* dist/ Klasörü: Üretilen son ürünün (Dağıtım Paketi) bulunduğu yerdir. Son kullanıcıya ulaştıracağınız .exe veya yürütülebilir klasör yapısı tam olarak buradadır.
* .spec Dosyası: Derleme ayarlarının kalıcı olarak kaydedildiği spesifikasyon dosyasıdır. Gelecekte aynı ayarlar ile tekrar derleme yapmak için kullanılabilir.

---

## 7. Hata Yönetimi ve Sistem Ayıklama (Debugging)

Derleme süreci, donanım kaynaklarına yüksek oranda bağımlı ağır bir işlemdir. Olası hataları yakalamak için şu adımlar izlenmelidir:

1. Eksik Kütüphane (ModuleNotFoundError): Eğer TCompile bu hatayı fırlatıyorsa, .tsharp projenizde kullandığınız bir modül (örneğin PySide6) sisteminizde yüklü değildir. İlgili kütüphaneyi PIP aracılığıyla kurun.
2. Karakter Kodlama Sorunları (UnicodeDecodeError): Dosyaların UTF-8 formatında kaydedildiğinden emin olun. TCompile, Türkçe karakter desteği için sistem genelinde standartlaştırılmış UTF-8 zorunluluğu arar.
3. Yetki Sınırları: Derleme yaparken yönetici/root haklarına sahip olup olmadığınızı kontrol edin, özellikle C: sürücüsündeki korumalı klasörlerde çalışıyorsanız.

---

## 8. Siber Güvenlik, Tersine Mühendislik ve Yanlış Alarmlar

TCompile, kaynak kodu derleyerek okunabilirliğini ortadan kaldırır. Üretilen çalıştırılabilir dosyalar doğrudan makine koduna yakın bir formattadır. Ancak, bu durum yüzde yüz bir şifreleme (obfuscation) sağlamaz. Hassas verilerin (örneğin veritabanı şifreleri, API anahtarları) doğrudan kaynak kod içine (hardcoded) yazılmaması tavsiye edilir.

Anti-Virüs Yanlış Alarmları (False Positives): PyInstaller tabanlı tüm derleyicilerde olduğu gibi, TCompile ile üretilen çalıştırılabilir dosyalar Windows Defender veya diğer güvenlik yazılımları tarafından bilinmeyen yayıncı (Unknown Publisher) sebebiyle şüpheli olarak işaretlenebilir. Bu durum yazılımın zararlı olduğu anlamına gelmez. Kodlarınızı dijital bir sertifika ile imzalayarak (Code Signing) bu sorunu çözebilirsiniz.

---

## 9. Lisans ve Dağıtım Koşulları

TCompile modülü ve tüm T-Sharp ekosistemi GNU Affero General Public License v3.0 (AGPL-3.0) lisansı ile korunmaktadır. 

Bu derleyici motoru kullanılarak paketlenen ve ticari veya ücretsiz olarak dağıtılan tüm yazılımlar, bu lisansın katı gerekliliklerine tabidir. Bir ağ üzerinden servis olarak sunulan uygulamalar da dahil olmak üzere, T-Sharp ekosisteminin değiştirilmiş her sürümünün kaynak kodu kullanıcıların erişimine açık tutulmalı ve aynı özgürlük felsefesiyle paylaşılmalıdır.

---
TCompile: Algoritmaları gerçeğe, Türkçe kodları bağımsız yazılımlara dönüştüren köprü.
