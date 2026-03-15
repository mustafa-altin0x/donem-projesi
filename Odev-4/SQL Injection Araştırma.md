1. SQL Injection Nedir?
SQL Injection (SQLi), web uygulamalarının kullanıcıdan aldığı verileri hiçbir filtreleme veya temizleme işleminden geçirmeden doğrudan veritabanı sorgularına dahil etmesi sonucu oluşan kritik bir güvenlik zafiyetidir.

Saldırgan, uygulamanın girdi alanlarına (login formları, arama çubukları, URL parametreleri vb.) zararlı SQL komutları enjekte ederek veritabanı motorunu manipüle eder ve normal şartlarda erişemeyeceği verilere ulaşır.

2. Saldırı Nasıl Yapılır? (Çalışma Mantığı)
Saldırının temel mantığı, sistemin arka planda çalıştırdığı sorgunun yapısını bozup onu farklı bir amaca hizmet edecek şekilde yeniden şekillendirmektir.

Klasik Bir Örnek (Kimlik Doğrulama Atlama / Bypass):
Arka planda çalışan normal bir giriş sorgusu şu şekildedir:
SELECT * FROM Kullanicilar WHERE username = '$kullanici_adi' AND password = '$sifre'

Saldırgan, kullanıcı adı formuna ' OR '1'='1 yazar. Şifre kısmını ise rastgele doldurur. Sistem kullanıcının girdiği bu veriyi doğrudan koda eklediğinde sorgu şu hale dönüşür:
SELECT * FROM Kullanicilar WHERE username = '' OR '1'='1' AND password = ''

Veritabanı bu sorguyu okurken OR '1'='1' kısmını görür. Bilgisayar mantığında 1 her zaman 1'e eşit olduğu için (TRUE), sorgunun geri kalanına veya şifrenin doğruluğuna bakmaksızın bu durumu "Doğru" kabul eder. Böylece saldırgan, şifresini bilmediği bir hesaba (genellikle tablodaki ilk kullanıcı olan Admin hesabına) giriş yapmış olur.

3. Zafiyet Türleri Nelerdir?
In-Band SQLi (Klasik): Saldırganın zararlı kodu gönderdiği ve sonucu aynı ekranda (örneğin web sayfasında) doğrudan gördüğü en yaygın türdür. Hata tabanlı (Error-based) veya verileri birleştirme tabanlı (Union-based) olabilir.

Inferential / Blind SQLi (Kör SQLi): Uygulama ekrana SQL hatası veya veri basmaz. Saldırgan, veritabanına "Doğru/Yanlış" soruları sorar veya sunucuyu bilerek geciktirerek (Time-based) gelen tepkilere göre içerideki veriyi harf harf tahmin eder. Uzun sürer ama son derece tehlikelidir.

Out-of-Band SQLi: Sunucunun cevap vermediği veya çok yavaş olduğu durumlarda kullanılır. Saldırgan, veritabanının içindeki verileri HTTP veya DNS istekleri aracılığıyla kendi kontrolündeki başka bir sunucuya göndermesini sağlar.

4. Zafiyetin Etkileri ve Sonuçları Nelerdir?
Veri İfşası (Data Breach): Kullanıcı şifreleri, kredi kartı numaraları, kişisel yazışmalar ve şirket sırları gibi tüm veritabanı dışarı sızdırılabilir.

Veri Manipülasyonu: Saldırgan sistemdeki kritik kayıtları silebilir (DROP TABLE), var olan verileri değiştirebilir (UPDATE) veya kendine yeni bir tam yetkili yönetici hesabı açabilir (INSERT).

Yetki Yükseltme ve Sunucu Ele Geçirme: Veritabanı kullanıcısının ayarları yeterince sıkı yapılandırılmamışsa, ileri düzey SQLi saldırıları ile veritabanı üzerinden işletim sisteminde komut çalıştırılarak tüm sunucu ele geçirilebilir (RCE).
