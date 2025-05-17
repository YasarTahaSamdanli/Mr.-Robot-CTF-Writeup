# Mr.-Robot-CTF-Writeup
Mr.Robot CTF

12 Mayıs 2025 Pazartesi

18:26

**🔧**** VPN & Hedefe Bağlantı**

2.  İlk başta TryHackme de Openvpn açtım ve hedef makinası açtık
3.  Terminale "openvpn ~/Downloads/yourname.ovpn" yazdık ki çalışsın

![resim](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/openVpn.png?raw=true)


1.  Hedef ip mizi öğrendik (TryHackme sitesinde veya hedef makinada).
2.  Nmap tool u ile açık portları tarayıp hizmet versiyonlarını öğrenmeye çalıştık.
3.  **********************Komut Açıklaması************************
4.  nmap -sV -p- <target-ip>  kodu ne işe yarar tek tek aşağıda açıklayayım.
5.  -sV: Servis ve versiyon bilgilerini öğrenir. -p-: 1-65535 arasındaki tüm TCP tarar.
6.  80 (HTTP) - Web sunucusu , 443 (HTTPS) -Güvenli Web, 22 (SSH) güvenlü Bağlantı.

![resim](![image](https://github.com/user-attachments/assets/edc378fb-54d4-4ac9-8bd8-f3710232ff5a)
)

**📌**** 1\. Bilgi Toplama**

1.  Şimdi ne yapmalısın ? Açıklayalım.
2.  Eğer 80 veya 443 açıksa tarayıcıda <target-ip>'yi aç ve sitenin içeriğine göz at.
3.  Kaynak koduna (Ctrl + U Tarayıcı içinde) veya curl (terminal içinde) gibi komutlarla ana sayfa içeriğine bak.
4.  Şimdi gobuster toolu ile wordlisteki her kelimeyi denicez uygun dizin bulucaz(Web dizin Taraması)

1.  ****************************Komut Açıklaması********************************
2.  Komut: "gobuster dir -u http://<hedef-ip> -w /usr/share/wordlists/dirb/common.txt" Tek tek açıklayalım.
3.  Gobuster: web sitesine istekler göndererek, gizli dizinleri ve dosyaları bulmaya çalışır.
4.  Dir: toolun mod seçimidir  dizin keşfi yapar. Başka mod örnekleri(dns,vhost,s3,fuzz) gibi.
5.  -u:  tarama yapılacak hedefin URL'si belirtilir. (-u http://<hedef_ip>).
6.  -w: wordlist demektir, tarama sırasında denenecek dizin adı içerir.
7.  Common.txt: En çok kullanılan dizin isimlerini içerir(admin,login,wp-content,images,vb).
8.  /usr/share/wordlists/dirb/common.txt: dosyasındaki her satırı tek tek alır.
9.  Bu url lere tek tek GET requestler 200,301,403 yanıtana göre gerçekten varmı bakar.

Tarayıcada Target İp açınca

![resim](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/ipa%C3%A7%C4%B1nca.png?raw=true)

**🌐**** Web Dizin Taraması**

Gobuseter çıktısı

![resim](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/gobuster%20%C3%A7%C4%B1kt%C4%B1s%C4%B1.png?raw=true)

1.  Peki şimdi ne yapıcaz?
2.  "curl <http://10.10.190.47/robots.txt> " gibi komutlarla terminale robots.txt sini açabiliz veya wget ile kendi makinenize indirebilirsiniz ("wget <http://10.10.190.47/intro>"). Ya da Tarayıcıda aç.
3.  Robots dosyasını açtık ve içinde key ve fsocity diye dosya çıktı onları pc ye indirdim wget le
4.  Sonraki adım da gobusterla belirlediğimiz robots dizisini bakıcaz ve nasıl bakıcamızı göstericem

![](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/robotsdosyas%C4%B1_%C3%A7%C4%B1k%C4%B1t%C4%B1s%C4%B1Ve_wget.png?raw=true)

Belki farkettiniz, hedef ıp değişti çünkü tryhackme çok internet yediği için hedef makinayı sanal çalıştırıp birbirine bağladım ana makinema.

1.  Fsocity.dir de şifre sandığım şeyler çıktı ilk baş brufe force ile çok denemeler yaptım. Key  dosyası da zaten 1. flagı verdi.
2.  Şimdi aslında fsocity.dic dosyasında şifre ve kullanıcı adı çıkarmaya çalıştım ama olmadı. Sonra farkettim ki statusu 200 olan license olan bir dizi var oradaymış aslında :D.

![](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/kullanc%C4%B1%20ad%C4%B1%20%C5%9Fifre.png?raw=true)

1.  Licesnse dizisinde bize bir base64 ile decode layacağımız bir şifre verdi base64 ile açtık.

 "echo şifre | base64 -d"

1.  Login dizisine gittik ve giriş yaptık

![](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/login.png?raw=true)

![](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/phpdinleme.png?raw=true)

1.  Editor kısmından 404 template kısmında Hazır bir kod kullandık :D Link <https://github.com/pentestmonkey/php-reverse-shell>
2.  Yani bir sunucuda çalıştırıldığında, *o sunucudan senin bilgisayarına bir bağlantı başlatır* ve senin bilgisayarından sunucuya komut verme yetkisi sağlar. (PHP reverse shell )
3.  Nc -lvnp 1234 ile bağlanıyoruz  ve shell açılıyor.(Bağlanması için tarayıca 404.php aç)

![](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/i%C3%A7erde.png?raw=true)

1.  İçeri girince yapıcak her şey fotoğrafta. Key 2 yi alcaz ama yetkimiz yetmiyor onun için kullanıcı değiştircez adı robot ama şifresi (hashcatli). Bu hashcatin modunu öğrenmemiz lazım ki kıralım :D

**🧑‍💻**** Yetki Yükseltme (Privilege Escalation)**

![](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/hash.png?raw=true)

1.  Şimdi bu hash kodunu kıran web siteleri var ama biz kendimiz kıracaz bunun için hashcat kullancaz
2.  Kırmadan önce hash.txt adında file yapıp içine atıcaz.
3.  *********************komut Açıklaması********************
4.  Hashcat -m 0 -a 0 -o found.txt hash.txt /usr/share/wordlists/rockyou.txt
5.  -m 0 (MD5) .  -a 0 dictionary attack. Rockyou.txt ise yüzlerce klasik şifreler olan dosya. -o found.txt ise bulduğu değeri found.txt ye atıcak

![](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/as%C4%B1l%20hash.png?raw=true)

1.  Şifreyi bulduk şimdi su komutu (switch user) kodu ile robot kullanıcısına gidicez.

![](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/su%C4%B0%C3%A7inDe%C4%9Fi%C5%9Fiklikler.png?raw=true)

**:shell'ini "tam özellikli" hale getirmek**. Yani **PTY (pse udo terminal)** almak.

KOD: python -c 'import pty; pty.spawn("/bin/bash")'

Password: abcdefchijklmnopqrstuvwxyz

1.  Ve evet robot dosyasındaki flag2 için yetkimiz yetiyor flagı alıcaz :D

![](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/2.flag.png?raw=true)

**🔥**** Root Yetkisi Alma**

1.  İkinci flagı da bulduk !!!! Şimdiki görevimiz yeniden yetki yükseltme olabilir :D çünkü dosyalara ulaşamıyoruz.
2.  ********************Kod açıklaması**************************
3.  find / -perm -4000 2>/dev/null
4.  Bütün dizinlerde SUID Açık olan dosyaları bul ve getir. Hata mesajlarını sil demek istiyoruz
5.  SUID: SUDI li bir programı çalıştırırsan, root gibi çalışır ve bize root a geçmemizi sağlayabilir.

![](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/perm%204000%20sui.png?raw=true)

1.  Burda shell için nmap kullandım çünkü versiyonu eskiydi ve açığı vardı.
2.  Bu açıklık yeni sürümlerde yoktur!!
3.  Nmap --interactive den sonra !sh yazıyoruz ve Root oluyoruz.

![](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/interactiveroot.png?raw=true)

1.  ****************Kod Açıklaması********************
2.  Eski sürümlü SUID nmap toolu ile içeri girip. (nmap --interactive)
3.  Root terminalı açıyoruz(!sh).

![](https://github.com/YasarTahaSamdanli/Mr.-Robot-CTF-Writeup/blob/main/3.flag.png?raw=true)

1.  Root olduktan sonra root dosyasına gidip 3. anahtarı alıyoruz.

**🎉**** Sonuç**

Tüm bayraklar başarıyla toplandı!

Bu süreçte CTF çözmenin ne kadar öğretici olduğunu bir kez daha gördüm. Her aşamasında hem teorik hem pratik bilgiler öğrendim.

Umarım notlarım başka birine de faydalı olur.
