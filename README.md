Inter-VLAN Routing (Router on a Stick) Mimarisi 🚀
Bu proje, kurumsal ağ altyapılarında departmanları (VLAN) birbirinden izole etmek ve bu izole ağları tek bir fiziksel yönlendirici (Router) üzerinden güvenli bir şekilde konuşturmak amacıyla hazırlanmış bir Cisco Packet Tracer simülasyonudur.

📌 Proje Amacı ve Senaryo
Bir şirket ağında IT ve Muhasebe departmanlarının ağ trafiğinin güvenliği ve performansı için birbirinden ayrılması (VLAN) sağlanmıştır. Ardından, bu iki farklı departmanın birbiriyle veya dış ağlarla iletişim kurabilmesi için "Router on a Stick" modeli kullanılarak 802.1Q trunking yapılandırılmıştır.

🧠 Teknik Detaylar ve Mantık
Neden VLAN Yapılandırdık?
Şirket içerisinde farklı departmanların (IT ve Muhasebe) ağ trafiğini birbirinden izole ederek veri güvenliğini sağladık. Bu sayede bir departmandaki olası bir ağ sorunu veya güvenlik açığı diğerini etkilemez.

Router on a Stick Mantığı
Fiziksel router üzerinde her VLAN için ayrı bir kablo çekmek yerine, tek bir fiziksel arayüzü (Gig0/0) mantıksal alt arayüzlere (sub-interfaces) böldük. Bu yöntem:

Maliyet Tasarrufu: Daha az fiziksel port kullanımı sağlar.

Esneklik: Yeni VLAN'lar eklemek fiziksel müdahale gerektirmez.

🛠️ Kullanılan Teknolojiler ve Protokoller
VLAN (Virtual Local Area Network): Ağ izolasyonu (VLAN 10 ve VLAN 20).

802.1Q Encapsulation: Trunk portlar üzerinden geçen trafiğin etiketlenmesi.

Sub-interfaces (Sanal Arayüzler): Router üzerinde tek fiziksel portun mantıksal olarak bölünmesi.

ICMP (Ping): Uçtan uca ağ erişilebilirliğinin test edilmesi.

📸 Topoloji ve Başarı Durumu
(Buraya yüklediğin topoloji görselinin linkini ekle)

(Buraya ping testinin başarılı olduğunu gösteren CMD görselinin linkini ekle)

💻 Yapılandırma Komutları Özeti
1. Switch Yapılandırması (VLAN ve Trunk)
Switch üzerinde departmanlar oluşturulmuş, ilgili portlar bu departmanlara atanmış ve Router'a giden port otoban (Trunk) haline getirilmiştir:

Bash
# VLAN'ların Oluşturulması
vlan 10
 name IT
vlan 20
 name Muhasebe

# Erişim Portlarının (Access) Ayarlanması
interface fastEthernet 0/1
 switchport mode access
 switchport access vlan 10
interface fastEthernet 0/2
 switchport mode access
 switchport access vlan 20

# Trunk Portun Ayarlanması
interface gigabitEthernet 0/1
 switchport mode trunk
2. Router Yapılandırması (Sub-interfaces)
Router üzerindeki tek bir fiziksel port, her VLAN için ayrı bir sanal ağ geçidine (Gateway) dönüştürülmüştür:

Bash
# Ana Portun Aktifleştirilmesi
interface gigabitEthernet 0/0
 no shutdown

# IT Departmanı (VLAN 10) İçin Alt Arayüz
interface gigabitEthernet 0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

# Muhasebe Departmanı (VLAN 20) İçin Alt Arayüz
interface gigabitEthernet 0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

 ## 🚀 v2.0 Güncellemesi: DHCP (Otomatik IP Dağıtımı) Entegrasyonu

Ağın büyümesi ve yeni cihazların eklenmesi senaryosuna karşılık, manuel IP atama (Statik IP) işlemi terk edilerek Router üzerinde bir DHCP sunucusu yapılandırılmıştır.

### Neden DHCP Kurduk?
* **Yönetim Kolaylığı:** Yüzlerce cihaza tek tek IP girmek yerine süreç otomatikleştirildi.
* **Hata Engelleme (IP Conflict):** İnsan kaynaklı aynı IP'yi iki cihaza verme hatalarının önüne geçildi.

### DHCP Yapılandırma Komutları
Router üzerinde her VLAN için ayrı IP havuzları oluşturulmuş ve ağ geçidi (Gateway) adresleri çakışmayı önlemek için dağıtım dışı bırakılmıştır:

```bash
# Gateway IP'lerini Dağıtımdan Çıkarma (Çakışma Önlemi)
ip dhcp excluded-address 192.168.10.1
ip dhcp excluded-address 192.168.20.1

# IT Departmanı İçin DHCP Havuzu
ip dhcp pool IT_HAVUZU
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8

# Muhasebe Departmanı İçin DHCP Havuzu
ip dhcp pool MUHASEBE_HAVUZU
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
---

## 🔒 v3.0 Güncellemesi: Uzaktan Güvenli Yönetim (SSH)

Gerçek dünya kurumsal ağ senaryolarında fiziksel cihazlara doğrudan müdahale etmek yerine uzaktan yönetim esastır. Bu güncelleme ile cihaz yönetim ağı güvence altına alınmıştır.

### Neden Telnet Yerine SSH Kullandık?
Telnet protokolü verileri (kullanıcı adı ve şifreler dahil) ağ üzerinde açık metin (clear-text) olarak iletir. Ağ dinleme (sniffing) saldırılarına karşı koymak için tüm veriyi kriptolojik olarak şifreleyen SSH (Secure Shell) yapılandırılmıştır.

### SSH Yapılandırma Komutları (Router)
Router üzerinde RSA anahtarları üretilmiş, lokal yönetici hesabı oluşturulmuş ve VTY (sanal terminal) portları sadece SSH kabul edecek şekilde ayarlanmıştır:

```bash
# Kimlik ve Domain Belirleme
hostname MerkezRouter
ip domain-name sirket.local

# RSA Şifreleme Anahtarının Üretilmesi (1024 bit)
crypto key generate rsa 

# En Üst Düzey Yetkili (Privilege 15) Yönetici Hesabı
username admin privilege 15 secret 123456

# Sanal Portların (VTY) Sadece SSH'a Açılması
line vty 0 4
 login local
 transport input ssh
