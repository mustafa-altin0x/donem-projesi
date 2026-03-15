- Ödev 1: Apache Path Traversal (CVE-2021-42013) Manuel Sömürü

--Zafiyetin Temel Mantığı :
Bu ödevde, Apache web sunucusunun 2.4.49 sürümünde ortaya çıkan çok kritik bir dizin atlatma (Path Traversal / LFI) zafiyetini inceledik. 
Olay aslında çok basit bir "gözden kaçırma" hikayesi. Normal şartlarda bir web sunucusunda gezinirken,
dışarıdan gelen bir kullanıcının sadece ona izin verilen klasörleri (örneğin sitenin anasayfasının olduğu `/var/www/html` dizinini) görmesi gerekir
Eğer birisi URL üzerinden `../` yazarak üst klasörlere, yani sistemin kök dizinine inmeye çalışırsa Apache bunu yakalar ve engeller.
Ancak bu spesifik sürümde yazılımcılar ufak ama ölümcül bir hata yapmış. Sistem, URL kodlamasını (URL encoding) tam olarak denetleyemiyor. 
Biz de filtreye takılmamak için doğrudan `../` yazmak yerine, bunun kodlanmış hali olan `%%32%65/` ifadesini kullanıyoruz.
Apache bu kodlanmış metni görünce zararlı olmadığını düşünüp geçiş izni veriyor.Bu sayede web dizininden çıkıp sistemin en dibine kadar
inebiliyor ve normalde okumaya yetkimiz olmayan gizli sistem dosyalarını okuyabiliyoruz.

---

--Laboratuvar ve Çalıştırma :
Bu zafiyeti güvenli bir şekilde test etmek ve mantığını kavramak için kendi bilgisayarımıza ekstra bir sunucu (Docker vb.) kurmuyoruz.
Çünkü bu ödevimide tüm işlemleri 'Hackviser'adlı siber güvenlik eğitim platformu üzerindeki hazır zafiyetli laboratuvar ortamından gerçekleştireceğiz.
Aşağıdaki adımları sırasıyla uygulayarak sistemi hackleyebilirsiniz:

### 1. Hedef Makineyi Başlatma (Keşif)
Öncelikle Hackviser platformuna giriş yapıp, ilgili Apache (CVE-2021-42013) zafiyet makinesini başlatın. Sistem ayağa kalktığında,
platform size saldırmanız için sanal bir *Hedef IP*  adresi verecektir . Bu adresi not alın.

### 2. Zafiyeti Sömürme (Exploitation)
Saldırıyı gerçekleştirmek için kendi bilgisayarınızdaki terminali  veya Hackviser'ın size sunduğu web terminalini açın. 
Sunucudaki o URL kodlama filtresini atlatmak için terminalden doğrudan şu `curl` komutunu fırlatıyoruz:

```bas
curl -s -X POST "http://<HEDEF_IP>/cgi-bin/%%32%65%%32%65/%%32%65%%32%65/%%32%65%%32%65/%%32%65%%32%65/bin/sh"
 -d "echo Content-Type: text/plain; echo; cat /secret.txt"
