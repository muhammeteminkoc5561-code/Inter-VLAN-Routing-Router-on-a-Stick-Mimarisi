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