## Ödev 2: Kendi Zafiyet Laboratuvarımızı Kurmak ve Manuel Sömürü (CVE-2021-42013)

-- 1. Amacımız ve İşin Felsefesi
Önceki ödevde Hackviser üzerindeki hazır ortamı kullanarak zafiyeti sömürmüştük. Ancak bu ödevde bizden asıl beklenen şu: "Hazır ortamda tetiği çekmek kolay,
peki bu zafiyetli hedefi sıfırdan kendi bilgisayarında kurabilir miyiz?"
Şimdi Bu doğrultuda, Hackviser'daki o hazır makineyi kullanmak yerine kendi özel laboratuvarımızı 'Docker' teknolojisiyle sıfırdan inşa edicez. İçine bilerek hatalı bir Apache sürümü (2.4.50) kurduk, kök dizinine gizli bir dosya sakladık ve konfigürasyonunu bozarak zafiyeti ellerimizle yarattık.

 NOT: Bu laboratuvarı kendi bilgisayarınızda kurmak için kodlarla boğuşmanıza veya sıfırdan reçete yazmanıza gerek yok. Sizler için yapılandırması tamamen hazır bir `Dockerfile` belgesini donem-projesi->Odev-2 klasörüne (repoya) yükledim incelemek isterseniz bakabilirisiniz. 
 
---

--2. Özel Zafiyet Laboratuvarı Kurulumu ve Manuel Sömürü 

Terminalinizi (CMD) açıp, indirdiğiniz bu `Dockerfile` dosyasının bulunduğu klasöre gidin ve makineyi inşa etmek için şu komutu çalıştırın :
```bash
docker build -t zafiyetli-apache .
```
Bu komutla kurban makineyi inşa etmiş olduk şimdide gelelim bu makineyi çalıştırmaya onun için ise yazacağımız kod:
```bash
docker run -d -p 0.0.0.0:8080:80 --name kurban-makine zafiyetli-apache
```
İstersek 0.0.0.0 yazıp risk oluşturacağımıza cıhazımıızn IP( kurban cihazın yani dockerın olduğu ağın IP  ) adresinide yazabilirdik.Tarayıcınızdan http://localhost:8080 adresine giderek "It works!" yazısını gördüyseniz hedef makine başarıyla çalışıyor demektir.

Şimdi sıra saldırı girişiminde  ilk etapta hedefi ayağa kaldırdıktan sonra doğrudan Uzaktan Komut Çalıştırma (RCE) yöntemiyle sızmaya çalıştım. Ancak /bin/sh (komut satırı) üzerinden gönderdiğim komutumu , sunucu /bin/sh programının kendisini (bir exe dosyası gibi) kopyalayıp bize indirmeye çalıştı, bana terminali kilitleyen anlamsız "binary"  veri yığınları döndürdü.Terminal bu ham program kodlarını okuyamadığı için kilitlendi.RCE (Komut çalıştırma) bizi yorduğu için sömürdüğümüz zafiyetin en saf haline, yani Doğrudan Dizin Atlatma (Path Traversal) saldırısına geri döndüm. Aracıları devreden çıkarıp doğrudan kök dizine sakladığım /secret.txt dosyasına yöneldim.Kullandığım nihai ve başarılı payload (LFI Yöntemi):

```bash
curl -s "http://172.20.10.2:8080/cgi-bin/%%32%65%%32%65/%%32%65%%32%65/%%32%65%%32%65/%%32%65%%32%65/%%32%65%%32%65/%%32%65%%32%65/%%32%65%%32%65/secret.txt"
```
Not:Komuta ekstra %%32%65%%32%65/ parçaları ekledim. Bu sayede sunucunun en dibinden en tepesine (kök dizine) kadar tırmanmayı garanti altına aldık.

