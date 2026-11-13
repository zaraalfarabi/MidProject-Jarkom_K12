# MidProject Jaringan Komputer - Kelompok 12

## Angota Kelompok

| Nama                         | NRP        |
| ---------------------------- | ---------- |
| Az Zahra Fiddien Al Farabi          | 5027251021 |
| M. Razan Azizi Djauhari | 5027251086 |
| Rheza Pramudita Adi Putra | 5027251090|

## 1. Topologi 
<img width="1384" height="633" alt="image" src="https://github.com/user-attachments/assets/5a41b99d-2e52-4e7d-ac44-2e41dc9a16c8" />

### 1.1 Backbone
Hubungan antar-router mengikuti Gambar 1 pada soal:

| Link | Router atas | Router bawah | Network | Media |
|---|---|---|---|---|
| L1 | R-PUSAT Se0/0/0 (.33) | R-DC Se0/0/0 (.34) | 10.96.241.32/30 | Serial |
| L2 | R-PUSAT Se0/0/1 (.37) | R-UTARA Se0/0/0 (.38) | 10.96.241.36/30 | Serial |
| L3 | R-PUSAT Se0/1/0 (.41) | R-SELATAN Se0/0/0 (.42) | 10.96.241.40/30 | Serial |
| L4 | R-PUSAT Se0/1/1 (.45) | R-TIMUR Se0/0/0 (.46) | 10.96.241.44/30 | Serial |
| L5 | R-PUSAT Se0/2/0 (.49) | R-BARAT Se0/0/0 (.50) | 10.96.241.48/30 | Serial |
| L6 | R-UTARA Se0/0/1 (.53) | R-PELABUHAN Se0/0/0 (.54) | 10.96.241.52/30 | Serial |
| ISP | R-PUSAT Gi0/0 (.49) | R-ISP Gi0/0 (.50) | 203.0.113.48/30 | Copper cross-over |
| Internet | R-ISP Gi0/1 (8.8.8.1) | SRV-INTERNET (8.8.8.8) | 8.8.8.0/24 | Copper cross-over |

R-PUSAT (Balai Kota) menjadi pusat topologi bintang yang terhubung ke R-DC, R-BARAT, R-UTARA, R-TIMUR, dan R-SELATAN. R-PELABUHAN tidak terhubung langsung ke R-PUSAT, melainkan melalui R-UTARA (L6), sesuai Gambar 1.

### 1.2 Desain setiap lokasi

| Router | Port | Switch | Perangkat perwakilan | Media | Network |
|---|---|---|---|---|---|
| R-PUSAT | Gi0/1 | SW-PEGAWAI-BALAIKOTA | PC-PEGAWAI-BALAIKOTA | Kabel | 10.96.224.0/21 |
| R-PUSAT | Gi0/2 | SW-SERVER-BALAIKOTA | SRV-BALAIKOTA | Kabel | 10.96.241.0/27 |
| R-DC | Gi0/0 | SW-CCTV-KOTA | PC-CCTV-KOTA | Kabel | 10.96.96.0/19 |
| R-DC | Gi0/1 | SW-SERVER-DC | SRV-DC | Kabel | 10.96.239.0/24 |
| R-UTARA | Gi0/0 | SW-WIFI-UTARA | AP-WIFI-UTARA + LAPTOP-WIFI-UTARA | Wireless | 10.96.0.0/18 |
| R-UTARA | Gi0/1 | SW-SENSOR-LALIN-UTARA | PC-SENSOR-LALIN-UTARA | Kabel | 10.96.236.0/23 |
| R-PELABUHAN | Gi0/0 | SW-KANTOR-PELABUHAN | PC-KANTOR-PELABUHAN | Kabel | 10.96.240.0/25 |
| R-PELABUHAN | Gi0/1 | SW-SCADA-PELABUHAN | PC-SCADA-PELABUHAN | Kabel | 10.96.160.0/19 |
| R-SELATAN | Gi0/0 | SW-WIFI-SELATAN | AP-WIFI-SELATAN + SP-WIFI-SELATAN | Wireless | 10.96.64.0/19 |
| R-SELATAN | Gi0/1 | SW-PJU-SELATAN | PC-PJU-SELATAN | Kabel | 10.96.240.128/25 |
| R-TIMUR | Gi0/0 | SW-WIFI-TIMUR | AP-WIFI-TIMUR + LAPTOP-WIFI-TIMUR | Wireless | 10.96.128.0/19 |
| R-TIMUR | Gi0/1 | SW-SENSOR-BANJIR-TIMUR | PC-SENSOR-BANJIR-TIMUR | Kabel | 10.96.232.0/22 |
| R-BARAT | Gi0/0 | SW-WIFI-BARAT | AP-WIFI-BARAT + SP-WIFI-BARAT | Wireless | 10.96.192.0/19 |
| R-BARAT | Gi0/1 | SW-PUSKESMAS-SEKOLAH-BARAT | PC-PUSKESMAS-SEKOLAH-BARAT | Kabel | 10.96.238.0/24 |

### 1.3 Pertimbangan dan alasan desain

| Komponen | Pilihan | Alasan |
|---|---|---|
| **Router** | Cisco 2911 untuk semua router | Memiliki 3 port Gigabit bawaan dan 4 slot modul, serta mendukung semua fitur yang dibutuhkan soal: static routing, DHCP, NAT, tunnel GRE, OSPF, dan IPsec (lisensi securityk9). |
| **Penambahan port router** | Modul **HWIC-2T** (2 port serial per modul) | Port bawaan tidak cukup. R-PUSAT membutuhkan 8 interface (5 link backbone, 1 link ISP, 2 LAN), sehingga dipasang 3 modul (eHWIC 0–2). R-UTARA dipasang 2 modul untuk L2, L6, dan L7 (link cadangan soal 7). Router lain cukup 1 modul (eHWIC 0). |
| **Media backbone** | Kabel **serial** | Mewakili link WAN antar wilayah kota yang berjauhan. Sisi DCE ditempatkan di router atas (R-PUSAT untuk L1–L5, R-UTARA untuk L6) dan diberi `clock rate`. |
| **Link ke ISP dan internet** | Kabel **copper cross-over** | R-PUSAT–R-ISP adalah sambungan router ke router, dan R-ISP–SRV-INTERNET adalah router ke server tanpa switch, sehingga menggunakan cross-over. |
| **Media LAN** | Kabel **copper straight-through** | Menghubungkan perangkat berbeda jenis: port Gigabit router → port Gi0/1 switch, lalu port Fa0/1 switch → perangkat. |
| **Switch** | 1 × Cisco 2960 per LAN (14 switch) | Setiap LAN adalah satu broadcast domain tersendiri. Switch menyediakan tempat untuk menambah host hingga jumlah penuh dan dapat dikembangkan bertingkat. |
| **LAN WiFi Publik** (Utara, Selatan, Timur, Barat) | **Access Point** + perangkat **wireless** | Pengguna WiFi publik menggunakan perangkat mobile. Laptop dipasang modul WPC300N, smartphone sudah memiliki WiFi bawaan. Setiap wilayah memakai SSID sendiri: `WiFi-Utara-K12`, `WiFi-Selatan-K12`, `WiFi-Timur-K12`, `WiFi-Barat-K12`. |
| **LAN sensor, CCTV, SCADA, dan PJU** | PC kabel sebagai perwakilan | Perangkat IoT tersebut terpasang tetap sehingga menggunakan kabel. PC dipilih sebagai perwakilan karena dapat menjalankan ping, tracert, dan FTP untuk pengujian. |
| **LAN Pegawai Balai Kota, Kantor Pelabuhan, Puskesmas dan Sekolah** | PC kabel | Mewakili workstation kantor yang bersifat tetap. |
| **LAN Server** | Server dengan **IP statis** | Soal mewajibkan server memakai IP statis. **SRV-BALAIKOTA (10.96.241.10)** menjalankan layanan web. **SRV-DC (10.96.239.10)** menjalankan DNS, DHCP untuk CCTV Kota, FTP, dan email. |
| **Jumlah perangkat per LAN** | 1 perangkat perwakilan | Sesuai ketentuan soal. Perhitungan subnet tetap menggunakan jumlah host penuh. |

### 1.4 Penamaan dan label
- Semua nama bawaan perangkat diganti dengan nama yang menunjukkan lokasinya: `R-<WILAYAH>` untuk router, `SW-<LAN>` untuk switch, `AP-WIFI-<WILAYAH>` untuk access point, `PC-<LAN>`, `LAPTOP-WIFI-<WILAYAH>`, dan `SP-WIFI-<WILAYAH>` untuk perangkat pengguna, serta `SRV-<LOKASI>` untuk server.
- Setiap LAN diberi label berisi nama subnet, Network ID/prefix, dan jumlah host, contoh: `CCTV Kota | 10.96.96.0/19 | 6.000 host`.
- Setiap link backbone diberi label nama link dan network-nya, contoh: `L1 | 10.96.241.32/30`.

### 1.5 Ringkasan perangkat

| Perangkat | Model | Jumlah |
|---|---|---|
| Router | Cisco 2911 | 8 |
| Modul serial | HWIC-2T | 10 |
| Switch | Cisco 2960-24TT | 14 |
| Access Point | AccessPoint-PT | 4 |
| PC | PC-PT | 8 |
| Laptop | Laptop-PT (WPC300N) | 2 |
| Smartphone | Smartphone-PT | 2 |
| Server | Server-PT | 3 |


## 2. Blok Alamat dan Pemilihan Skema

Link spreadsheet tabel: https://docs.google.com/spreadsheets/d/1NvaxmlzC8IM6mpqm_pLIWuHrr4Bjr4hK/edit?usp=sharing&ouid=115783535153531900641&rtpof=true&sd=true

### 2.1 Blok alamat kelompok
Sesuai aturan soal, oktet ke-2 = 8 × nomor kelompok = 8 × 12 = **96**, sehingga blok alamat Kelompok 12 adalah **10.96.0.0**.

Rentang privat yang dipakai adalah **10.0.0.0/8**, karena:
- Oktet ke-2 bernilai 96 hanya mungkin di rentang 10.0.0.0/8. Rentang 172.16.0.0/12 hanya memiliki oktet ke-2 antara 16–31, sedangkan 192.168.0.0/16 oktet ke-2-nya selalu 168.
- Total kebutuhan alamat seluruh subnet adalah **61.760 alamat**, sehingga membutuhkan minimal blok /16. Ukuran ini terlalu besar untuk 192.168.0.0/16.
- Alamat privat tidak dirutekan di internet, sehingga akses ke server 8.8.8.8 dilakukan melalui NAT di R-PUSAT.

### 2.2 Perbandingan VLSM dan CIDR

| Metode | Prefix terkecil yang menampung | Ukuran blok | Pemakaian | Alamat terbuang |
|---|---|---|---|---|
| **VLSM** | 10.96.0.0/16 | 65.536 | **94,24%** | 3.776 |
| CIDR | 10.96.0.0/15 | 131.072 | 47,12% | 69.312 |

### 2.3 Alasan memilih VLSM
Kelompok kami memilih **VLSM** untuk diimplementasikan di Packet Tracer, dengan alasan:

1. **Paling efisien dalam pemakaian alamat.**
   VLSM memotong blok sesuai kebutuhan setiap subnet dan menyusunnya berurutan dari yang terbesar, sehingga sisa alamat hanya berasal dari pembulatan ke 2ⁿ. Hasilnya 94,24% alamat terpakai dan seluruh jaringan kota muat dalam satu blok **/16**. Sebaliknya, CIDR mengharuskan setiap router memiliki satu blok supernet berukuran 2ⁿ, misalnya subnet /19 dan /25 di satu router harus digabung menjadi /18. Akibatnya banyak ruang kosong dan total kebutuhan naik menjadi /15 dengan pemakaian hanya 47,12% (selisih 47,12 poin).

2. **Paling konsisten dengan lembar jawaban UTS.**
   Urutan alokasi VLSM di lembar UTS kelompok sudah benar (subnet terbesar dialokasikan lebih dulu) dan tujuh subnet pertama tidak perlu diubah. Kesalahan hanya terjadi pada network ID mulai subnet ke-8 dan sudah dicatat di tabel revisi. Dengan VLSM, perubahan dari UTS paling sedikit sehingga implementasi tetap konsisten dengan perhitungan.

3. **Ruang alamat tersisa tetap rapi untuk pengembangan.**
   Seluruh subnet berada di 10.96.0.0 – 10.96.241.63, dan link tambahan Mid-Project (L7 dan Tunnel0) dapat ditempatkan langsung setelah link L6 tanpa menggeser subnet lain.

**Konsekuensi yang kami terima:** karena alamat LAN milik satu router tidak berdekatan (contoh: CCTV Kota 10.96.96.0/19 dan Server dan Storage DC 10.96.239.0/24, keduanya di R-DC), rute tidak dapat diringkas per router. R-PUSAT memerlukan satu static route untuk setiap LAN di bawahnya (dua rute per router).

## 3. Konfigurasi Dasar dan IP Address Interface

Sebelum routing dikonfigurasi, setiap router diberi **hostname** dan **alamat IP** pada setiap port yang dipakai, lalu port dinyalakan. Tahap ini menjadi dasar untuk soal 3: router hanya mengenal jaringan yang terhubung langsung (*connected*), sehingga link antar-router harus hidup lebih dulu sebelum static route dipasang.

### 3.1 Aturan pengalamatan
- **Gateway LAN** = host pertama setiap subnet VLSM (contoh: CCTV Kota 10.96.96.0/19 → gateway 10.96.96.1).
- **Link antar-router /30**: router atas memakai host pertama, router bawah memakai host kedua (contoh: L1 10.96.241.32/30 → R-PUSAT .33, R-DC .34).
- **Sisi DCE** kabel serial (R-PUSAT untuk L1–L5, R-UTARA untuk L6) diberi `clock rate 2000000`.
- **Link ISP** memakai IP publik simulasi 203.0.113.48/30 (R-PUSAT .49, R-ISP .50).

### 3.2 Arti perintah

| Perintah | Fungsi |
|---|---|
| `enable` | Masuk mode privileged (`Router#`) |
| `configure terminal` | Masuk mode konfigurasi global (`Router(config)#`) |
| `hostname R-PUSAT` | Mengganti nama router |
| `no ip domain-lookup` | Mencegah CLI menunggu lama saat salah ketik perintah |
| `interface …` | Memilih port yang dikonfigurasi |
| `description …` | Catatan fungsi port |
| `ip address <IP> <mask>` | Memberi alamat IP pada port |
| `clock rate 2000000` | Sinyal clock pada sisi DCE kabel serial |
| `no shutdown` | Menyalakan port (port router Cisco mati secara default) |
| `write memory` | Menyimpan konfigurasi ke NVRAM |

### 3.3 Konfigurasi setiap router

**R-PUSAT**
```
enable
configure terminal
hostname R-PUSAT
no ip domain-lookup
interface GigabitEthernet0/0
 description Link ke R-ISP
 ip address 203.0.113.49 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 description LAN Pegawai Balai Kota
 ip address 10.96.224.1 255.255.248.0
 no shutdown
interface GigabitEthernet0/2
 description LAN Server Balai Kota
 ip address 10.96.241.1 255.255.255.224
 no shutdown
interface Serial0/0/0
 description L1 ke R-DC
 ip address 10.96.241.33 255.255.255.252
 clock rate 2000000
 no shutdown
interface Serial0/0/1
 description L2 ke R-UTARA
 ip address 10.96.241.37 255.255.255.252
 clock rate 2000000
 no shutdown
interface Serial0/1/0
 description L3 ke R-SELATAN
 ip address 10.96.241.41 255.255.255.252
 clock rate 2000000
 no shutdown
interface Serial0/1/1
 description L4 ke R-TIMUR
 ip address 10.96.241.45 255.255.255.252
 clock rate 2000000
 no shutdown
interface Serial0/2/0
 description L5 ke R-BARAT
 ip address 10.96.241.49 255.255.255.252
 clock rate 2000000
 no shutdown
end
write memory
```

**R-ISP**
```
enable
configure terminal
hostname R-ISP
no ip domain-lookup
interface GigabitEthernet0/0
 description Link ke R-PUSAT
 ip address 203.0.113.50 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 description Gateway server internet 8.8.8.8
 ip address 8.8.8.1 255.255.255.0
 no shutdown
end
write memory
```

**R-DC**
```
enable
configure terminal
hostname R-DC
no ip domain-lookup
interface GigabitEthernet0/0
 description LAN CCTV Kota
 ip address 10.96.96.1 255.255.224.0
 no shutdown
interface GigabitEthernet0/1
 description LAN Server dan Storage DC
 ip address 10.96.239.1 255.255.255.0
 no shutdown
interface Serial0/0/0
 description L1 ke R-PUSAT
 ip address 10.96.241.34 255.255.255.252
 no shutdown
end
write memory
```

**R-UTARA**
```
enable
configure terminal
hostname R-UTARA
no ip domain-lookup
interface GigabitEthernet0/0
 description LAN WiFi Publik Utara
 ip address 10.96.0.1 255.255.192.0
 no shutdown
interface GigabitEthernet0/1
 description LAN Sensor Lalu Lintas Utara
 ip address 10.96.236.1 255.255.254.0
 no shutdown
interface Serial0/0/0
 description L2 ke R-PUSAT
 ip address 10.96.241.38 255.255.255.252
 no shutdown
interface Serial0/0/1
 description L6 ke R-PELABUHAN
 ip address 10.96.241.53 255.255.255.252
 clock rate 2000000
 no shutdown
end
write memory
```

**R-PELABUHAN**
```
enable
configure terminal
hostname R-PELABUHAN
no ip domain-lookup
interface GigabitEthernet0/0
 description LAN Kantor Pelabuhan
 ip address 10.96.240.1 255.255.255.128
 no shutdown
interface GigabitEthernet0/1
 description LAN Sensor dan SCADA Pelabuhan
 ip address 10.96.160.1 255.255.224.0
 no shutdown
interface Serial0/0/0
 description L6 ke R-UTARA
 ip address 10.96.241.54 255.255.255.252
 no shutdown
end
write memory
```

**R-SELATAN**
```
enable
configure terminal
hostname R-SELATAN
no ip domain-lookup
interface GigabitEthernet0/0
 description LAN WiFi Publik Selatan
 ip address 10.96.64.1 255.255.224.0
 no shutdown
interface GigabitEthernet0/1
 description LAN PJU Pintar Selatan
 ip address 10.96.240.129 255.255.255.128
 no shutdown
interface Serial0/0/0
 description L3 ke R-PUSAT
 ip address 10.96.241.42 255.255.255.252
 no shutdown
end
write memory
```

**R-TIMUR**
```
enable
configure terminal
hostname R-TIMUR
no ip domain-lookup
interface GigabitEthernet0/0
 description LAN WiFi Publik Timur
 ip address 10.96.128.1 255.255.224.0
 no shutdown
interface GigabitEthernet0/1
 description LAN Sensor Banjir Timur
 ip address 10.96.232.1 255.255.252.0
 no shutdown
interface Serial0/0/0
 description L4 ke R-PUSAT
 ip address 10.96.241.46 255.255.255.252
 no shutdown
end
write memory
```

**R-BARAT**
```
enable
configure terminal
hostname R-BARAT
no ip domain-lookup
interface GigabitEthernet0/0
 description LAN WiFi Publik Barat
 ip address 10.96.192.1 255.255.224.0
 no shutdown
interface GigabitEthernet0/1
 description LAN Puskesmas dan Sekolah Barat
 ip address 10.96.238.1 255.255.255.0
 no shutdown
interface Serial0/0/0
 description L5 ke R-PUSAT
 ip address 10.96.241.50 255.255.255.252
 no shutdown
end
write memory
```

### 3.4 Pengujian

Setiap link diuji dengan ping dari router atas ke IP router bawah:

| Dari | Perintah | IP milik | Link | Hasil |
|---|---|---|---|---|
| R-PUSAT | `ping 10.96.241.34` | R-DC Serial0/0/0 | L1 | <img width="999" height="135" alt="image" src="https://github.com/user-attachments/assets/77b571a3-6de0-4ff3-847a-e12250209500" />|
| R-PUSAT | `ping 10.96.241.38` | R-UTARA Serial0/0/0 | L2 |<img width="481" height="90" alt="image" src="https://github.com/user-attachments/assets/487091e4-0011-45f3-99ba-9940092478ea" />|
| R-PUSAT | `ping 10.96.241.42` | R-SELATAN Serial0/0/0 | L3 | <img width="481" height="90" alt="image" src="https://github.com/user-attachments/assets/8655279e-2adf-469a-8f79-e0b8cdca6416" />|
| R-PUSAT | `ping 10.96.241.46` | R-TIMUR Serial0/0/0 | L4 | <img width="481" height="90" alt="image" src="https://github.com/user-attachments/assets/cbc84383-01be-4499-842d-7ca5018583d3" />|
| R-PUSAT | `ping 10.96.241.50` | R-BARAT Serial0/0/0 | L5 | <img width="481" height="90" alt="image" src="https://github.com/user-attachments/assets/8bf3a479-1b4e-46c3-8e05-243aef8c9714" />|
| R-PUSAT | `ping 203.0.113.50` | R-ISP GigabitEthernet0/0 | ISP | <img width="718" height="132" alt="image" src="https://github.com/user-attachments/assets/7ffdcfe6-73fe-48d8-9a8b-e5fb7e78f089" />|
| R-UTARA | `ping 10.96.241.54` | R-PELABUHAN Serial0/0/0 | L6 | <img width="898" height="165" alt="image" src="https://github.com/user-attachments/assets/93c4e453-10bf-44e1-9d6a-a3cf19d3ce24" />|

> Link R-PUSAT ↔ R-ISP memakai 203.0.113.48/30 (R-PUSAT .49, R-ISP .50) sebagai IP publik simulasi. Blok 203.0.113.0/24 adalah alamat khusus dokumentasi (RFC 5737), sehingga tidak bentrok dengan alamat internet sungguhan dan tidak termasuk tabel subnetting VLSM kelompok.


## 4. Static Routing 

### 4.1 Konsep
Setelah Tahap 1, setiap router hanya mengenal jaringan yang **terhubung langsung** (*connected*). Agar LAN di wilayah berbeda dapat saling berkomunikasi, setiap router diberi **static route**, yaitu petunjuk manual: *"untuk mencapai jaringan X, kirim paket ke router Y (next-hop)"*.

Format perintah:
```
ip route <network tujuan> <subnet mask> <IP next-hop>
```

Aturan sesuai soal:
1. **Default route hanya mengarah ke router di atasnya / keluar.** `0.0.0.0 0.0.0.0` berarti "semua tujuan yang tidak dikenal".
   - R-DC, R-UTARA, R-SELATAN, R-TIMUR, R-BARAT → R-PUSAT
   - R-PELABUHAN → R-UTARA
   - R-PUSAT → R-ISP
2. **R-PUSAT dan R-UTARA wajib memiliki static route ke jaringan di bawahnya (ke dalam).**
   - R-PUSAT: ke semua LAN milik R-DC, R-UTARA, R-PELABUHAN, R-SELATAN, R-TIMUR, dan R-BARAT.
   - R-UTARA: ke semua LAN milik R-PELABUHAN.
3. **R-ISP tidak diberi rute apa pun** ke alamat privat (ketentuan soal 5).

```
                    R-ISP
                      ▲ default route
                   R-PUSAT ── rute ke dalam: 12 LAN + link L6
     ▲default   ▲default   ▲default   ▲default   ▲default
     R-DC      R-BARAT    R-UTARA    R-TIMUR   R-SELATAN
                             │ rute ke dalam: 2 LAN Pelabuhan
                             ▲ default route
                         R-PELABUHAN
```

### 4.2 Mengapa R-PUSAT membutuhkan satu rute per LAN?
Karena skema yang dipakai adalah **VLSM**, alamat dua LAN milik satu router tidak berdekatan sehingga tidak bisa diringkas menjadi satu rute. Contoh pada R-DC: CCTV Kota `10.96.96.0/19` dan Server dan Storage DC `10.96.239.0/24` berjauhan. Akibatnya R-PUSAT memerlukan **2 rute untuk setiap router bawah** (12 rute LAN), ditambah 1 rute ke link L6 dan 1 default route.

### 4.3 Tabel static route

| Router | Network tujuan | Next-hop | Keterangan |
|---|---|---|---|
| R-PUSAT | 10.96.96.0/19 | 10.96.241.34 | LAN CCTV Kota (R-DC) via L1 |
| R-PUSAT | 10.96.239.0/24 | 10.96.241.34 | LAN Server dan Storage DC (R-DC) via L1 |
| R-PUSAT | 10.96.0.0/18 | 10.96.241.38 | LAN WiFi Publik Utara (R-UTARA) via L2 |
| R-PUSAT | 10.96.236.0/23 | 10.96.241.38 | LAN Sensor Lalu Lintas Utara (R-UTARA) via L2 |
| R-PUSAT | 10.96.240.0/25 | 10.96.241.38 | LAN Kantor Pelabuhan, dititipkan ke R-UTARA |
| R-PUSAT | 10.96.160.0/19 | 10.96.241.38 | LAN Sensor dan SCADA Pelabuhan, dititipkan ke R-UTARA |
| R-PUSAT | 10.96.241.52/30 | 10.96.241.38 | Link L6 (R-UTARA – R-PELABUHAN) |
| R-PUSAT | 10.96.64.0/19 | 10.96.241.42 | LAN WiFi Publik Selatan (R-SELATAN) via L3 |
| R-PUSAT | 10.96.240.128/25 | 10.96.241.42 | LAN PJU Pintar Selatan (R-SELATAN) via L3 |
| R-PUSAT | 10.96.128.0/19 | 10.96.241.46 | LAN WiFi Publik Timur (R-TIMUR) via L4 |
| R-PUSAT | 10.96.232.0/22 | 10.96.241.46 | LAN Sensor Banjir Timur (R-TIMUR) via L4 |
| R-PUSAT | 10.96.192.0/19 | 10.96.241.50 | LAN WiFi Publik Barat (R-BARAT) via L5 |
| R-PUSAT | 10.96.238.0/24 | 10.96.241.50 | LAN Puskesmas dan Sekolah Barat (R-BARAT) via L5 |
| R-PUSAT | 0.0.0.0/0 | 203.0.113.50 | Default route ke R-ISP (internet) |
| R-UTARA | 10.96.240.0/25 | 10.96.241.54 | LAN Kantor Pelabuhan via L6 |
| R-UTARA | 10.96.160.0/19 | 10.96.241.54 | LAN Sensor dan SCADA Pelabuhan via L6 |
| R-UTARA | 0.0.0.0/0 | 10.96.241.37 | Default route ke R-PUSAT |
| R-PELABUHAN | 0.0.0.0/0 | 10.96.241.53 | Default route ke R-UTARA |
| R-DC | 0.0.0.0/0 | 10.96.241.33 | Default route ke R-PUSAT |
| R-SELATAN | 0.0.0.0/0 | 10.96.241.41 | Default route ke R-PUSAT |
| R-TIMUR | 0.0.0.0/0 | 10.96.241.45 | Default route ke R-PUSAT |
| R-BARAT | 0.0.0.0/0 | 10.96.241.49 | Default route ke R-PUSAT |
| R-ISP | — | — | Tidak ada rute ke alamat privat |

Penjelasan khusus:
- **LAN Pelabuhan di R-PUSAT** diarahkan ke **10.96.241.38 (R-UTARA)**, bukan langsung ke R-PELABUHAN, karena R-PUSAT tidak bertetangga langsung dengan R-PELABUHAN. R-UTARA yang kemudian meneruskan ke R-PELABUHAN.
- **Rute ke L6 (10.96.241.52/30)** dibutuhkan agar R-PUSAT dapat menjangkau alamat R-PELABUHAN di link L6 (10.96.241.54), yang juga dipakai sebagai tujuan tunnel GRE pada soal 6.

### 4.4 Konfigurasi setiap router

**R-PUSAT**
```
enable
configure terminal
ip route 10.96.96.0 255.255.224.0 10.96.241.34
ip route 10.96.239.0 255.255.255.0 10.96.241.34
ip route 10.96.0.0 255.255.192.0 10.96.241.38
ip route 10.96.236.0 255.255.254.0 10.96.241.38
ip route 10.96.240.0 255.255.255.128 10.96.241.38
ip route 10.96.160.0 255.255.224.0 10.96.241.38
ip route 10.96.241.52 255.255.255.252 10.96.241.38
ip route 10.96.64.0 255.255.224.0 10.96.241.42
ip route 10.96.240.128 255.255.255.128 10.96.241.42
ip route 10.96.128.0 255.255.224.0 10.96.241.46
ip route 10.96.232.0 255.255.252.0 10.96.241.46
ip route 10.96.192.0 255.255.224.0 10.96.241.50
ip route 10.96.238.0 255.255.255.0 10.96.241.50
ip route 0.0.0.0 0.0.0.0 203.0.113.50
end
write memory
```

**R-UTARA**
```
enable
configure terminal
ip route 10.96.240.0 255.255.255.128 10.96.241.54
ip route 10.96.160.0 255.255.224.0 10.96.241.54
ip route 0.0.0.0 0.0.0.0 10.96.241.37
end
write memory
```

**R-PELABUHAN**
```
enable
configure terminal
ip route 0.0.0.0 0.0.0.0 10.96.241.53
end
write memory
```

**R-DC**
```
enable
configure terminal
ip route 0.0.0.0 0.0.0.0 10.96.241.33
end
write memory
```

**R-SELATAN**
```
enable
configure terminal
ip route 0.0.0.0 0.0.0.0 10.96.241.41
end
write memory
```

**R-TIMUR**
```
enable
configure terminal
ip route 0.0.0.0 0.0.0.0 10.96.241.45
end
write memory
```

**R-BARAT**
```
enable
configure terminal
ip route 0.0.0.0 0.0.0.0 10.96.241.49
end
write memory
```

> Catatan: pada soal 6, dua rute LAN Pelabuhan di R-PUSAT (`10.96.240.0/25` dan `10.96.160.0/19` via 10.96.241.38) diubah menjadi rute cadangan (AD 5) dan ditambah rute utama melalui tunnel. Penjelasannya ada di bagian Tunnel GRE.

### 4.5 Pengujian

**a. Tabel routing**

| Router | Perintah | Hasil yang diharapkan | Bukti |
|---|---|---|---|
| R-PUSAT | `show ip route static` | 13 rute `S` ke LAN dan L6, serta `S* 0.0.0.0/0 via 203.0.113.50` | <img width="439" height="234" alt="image" src="https://github.com/user-attachments/assets/ba3ef229-74cd-4679-afdf-ea2081947ee3" />|
| R-UTARA | `show ip route static` | 2 rute ke LAN Pelabuhan dan `S* 0.0.0.0/0 via 10.96.241.37` | <img width="429" height="91" alt="image" src="https://github.com/user-attachments/assets/9cbc8e28-8e85-44ad-bf76-675d9a3f14d8" />|
| R-DC | `show ip route static` | `S* 0.0.0.0/0 via 10.96.241.33` | <img width="272" height="51" alt="image" src="https://github.com/user-attachments/assets/68664410-93ff-4d23-84d4-37cfce3dc8a2" />|

**b. Komunikasi antar LAN beda wilayah**

| Dari | Ke | Jalur yang dilewati | Bukti |
|---|---|---|---|
| PC di wilayah lain | PC-PEGAWAI-BALAIKOTA (10.96.224.2) | → R-PUSAT → LAN Pegawai | <img width="441" height="213" alt="image" src="https://github.com/user-attachments/assets/96d80dda-625d-43c6-9fce-f0debff43dcd" />|
| PC di wilayah lain | PC-CCTV-KOTA (10.96.96.2) | → R-PUSAT → R-DC → LAN CCTV | <img width="387" height="209" alt="image" src="https://github.com/user-attachments/assets/47070029-51c2-42a9-bb47-2962a49396d5" />|
| PC-KANTOR-PELABUHAN | SRV-DC (10.96.239.10) | R-PELABUHAN → R-UTARA → R-PUSAT → R-DC | <img width="757" height="493" alt="image" src="https://github.com/user-attachments/assets/c1666422-7623-4660-ab24-74ddb6c40906" />|
| PC-SENSOR-BANJIR-TIMUR | SRV-DC (10.96.239.10) | R-TIMUR → R-PUSAT → R-DC | <img width="783" height="472" alt="image" src="https://github.com/user-attachments/assets/3a046bed-214f-4c76-97e9-c6cbf078764d" />|

**c. Jalur paket**

| Dari | Perintah | Hasil | Bukti |
|---|---|---|---|
| R-PELABUHAN | `traceroute 10.96.224.1` | 10.96.241.53 (R-UTARA) → 10.96.241.37 (R-PUSAT): paket naik lewat default route sesuai hierarki | <img width="344" height="90" alt="image" src="https://github.com/user-attachments/assets/c20bfabc-6bbb-4485-bf52-982d6c912591" />|

Seluruh LAN dapat saling berkomunikasi hanya dengan static route dan default route, sesuai ketentuan soal 3.


## 5. DHCP

**DHCP** (*Dynamic Host Configuration Protocol*) adalah protokol yang membagikan konfigurasi jaringan secara otomatis kepada perangkat, yaitu alamat IP, subnet mask, default gateway, dan DNS server. Dengan DHCP, pengguna tidak perlu mengisi IP secara manual, dan risiko alamat ganda dapat dihindari.

Proses DHCP terdiri dari empat tahap (**DORA**):
1. **Discover** – perangkat mengirim broadcast untuk mencari server DHCP.
2. **Offer** – server DHCP menawarkan sebuah alamat IP.
3. **Request** – perangkat meminta alamat yang ditawarkan.
4. **Acknowledge** – server mengonfirmasi, dan perangkat mulai memakai alamat tersebut.

| Istilah | Penjelasan |
|---|---|
| **IP statis** | Alamat yang diisi manual dan tidak berubah; dipakai interface router dan server agar alamatnya selalu tetap. |
| **IP dinamis** | Alamat yang didapat otomatis dari server DHCP; dipakai perangkat pengguna. |
| **DHCP pool** | Kumpulan alamat yang boleh dibagikan untuk satu LAN, beserta gateway dan DNS-nya. |
| **Excluded address** | Alamat di dalam pool yang dilarang dibagikan, misalnya alamat gateway, agar tidak bentrok dengan IP statis. |
| **DHCP relay** (`ip helper-address`) | Fitur router untuk meneruskan permintaan DHCP (broadcast) ke server DHCP yang berada di jaringan lain, karena broadcast tidak bisa melewati router. |

Penerapan sesuai ketentuan soal:
- **IP statis:** semua interface router dan server (SRV-DC 10.96.239.10, SRV-BALAIKOTA 10.96.241.10, SRV-INTERNET 8.8.8.8).
- **IP dinamis:** semua perangkat pengguna mendapat IP, gateway, dan DNS **10.96.239.10** dari router lokalnya.
- **Tidak bentrok:** alamat gateway dikeluarkan dari pool dengan `ip dhcp excluded-address`, dan kedua LAN server tidak memiliki pool DHCP.
- **CCTV Kota:** dilayani **SRV-DC**, bukan router, melalui DHCP relay (`ip helper-address`) di R-DC.

### 5.1 Pool DHCP

| Router | Pool | Network | Gateway |
|---|---|---|---|
| R-PUSAT | PEGAWAI-BALAIKOTA | 10.96.224.0/21 | 10.96.224.1 |
| R-UTARA | WIFI-UTARA | 10.96.0.0/18 | 10.96.0.1 |
| R-UTARA | SENSOR-LALIN-UTARA | 10.96.236.0/23 | 10.96.236.1 |
| R-PELABUHAN | KANTOR-PELABUHAN | 10.96.240.0/25 | 10.96.240.1 |
| R-PELABUHAN | SCADA-PELABUHAN | 10.96.160.0/19 | 10.96.160.1 |
| R-SELATAN | WIFI-SELATAN | 10.96.64.0/19 | 10.96.64.1 |
| R-SELATAN | PJU-SELATAN | 10.96.240.128/25 | 10.96.240.129 |
| R-TIMUR | WIFI-TIMUR | 10.96.128.0/19 | 10.96.128.1 |
| R-TIMUR | SENSOR-BANJIR-TIMUR | 10.96.232.0/22 | 10.96.232.1 |
| R-BARAT | WIFI-BARAT | 10.96.192.0/19 | 10.96.192.1 |
| R-BARAT | PUSKESMAS-SEKOLAH-BARAT | 10.96.238.0/24 | 10.96.238.1 |
| **SRV-DC** | **CCTV-KOTA** | 10.96.96.0/19 (start .2, 6000 user) | 10.96.96.1 |

Semua pool memakai DNS server 10.96.239.10.

### 5.2 Konfigurasi

Contoh router (R-UTARA). Router lain sama polanya, sesuai tabel di atas:
```
ip dhcp excluded-address 10.96.0.1
ip dhcp excluded-address 10.96.236.1
ip dhcp pool WIFI-UTARA
 network 10.96.0.0 255.255.192.0
 default-router 10.96.0.1
 dns-server 10.96.239.10
ip dhcp pool SENSOR-LALIN-UTARA
 network 10.96.236.0 255.255.254.0
 default-router 10.96.236.1
 dns-server 10.96.239.10
```

DHCP relay untuk CCTV Kota di R-DC:
```
interface GigabitEthernet0/0
 ip helper-address 10.96.239.10
```
Permintaan DHCP dari PC-CCTV berupa broadcast yang tidak bisa menyeberang router. `ip helper-address` membuat R-DC meneruskannya ke SRV-DC, lalu SRV-DC memilih pool CCTV-KOTA berdasarkan gateway asal (10.96.96.1).

### 5.3 Bukti pengujian

Pengujian dilakukan pada lima perangkat perwakilan yang mencakup perangkat kabel dan wireless, DHCP dari router, serta DHCP dari SRV-DC. Setiap perangkat diuji dengan `ipconfig` (mendapat IP dari DHCP) dan `ping` ke gateway serta SRV-DC (10.96.239.10).

| No | Perangkat | Jenis | Sumber DHCP | IP dari DHCP | Gateway | Bukti |
|---|---|---|---|---|---|---|
| 1 | PC-PEGAWAI-BALAIKOTA | Kabel | R-PUSAT (10.96.224.1) | 10.96.224.2/21 | 10.96.224.1 | <img width="350" alt="DHCP PC Pegawai" src="https://github.com/user-attachments/assets/8a7ed7b3-862d-49f0-9224-3d5f3e5433db" /> |
| 2 | PC-CCTV-KOTA | Kabel | **SRV-DC (10.96.239.10)** | 10.96.96.2/19 | 10.96.96.1 | <img width="350" alt="DHCP PC CCTV" src="https://github.com/user-attachments/assets/e8fe676a-36fd-40c2-84fc-7ed9545544f5" /> |
| 3 | LAPTOP-WIFI-UTARA | Wireless | R-UTARA (10.96.0.1) | 10.96.0.2/18 | 10.96.0.1 | <img width="350" alt="DHCP Laptop WiFi Utara" src="https://github.com/user-attachments/assets/31e4ea67-477b-464c-91ca-0cea5d52e006" /> |
| 4 | SP-WIFI-SELATAN | Wireless | R-SELATAN (10.96.64.1) | 10.96.64.2/19 | 10.96.64.1 | <img width="350" alt="DHCP SP WiFi Selatan" src="https://github.com/user-attachments/assets/0926dd22-bb9e-471f-85d1-200a85840c03" /> |
| 5 | PC-KANTOR-PELABUHAN | Kabel | R-PELABUHAN (10.96.240.1) | 10.96.240.2/25 | 10.96.240.1 | <img width="350" alt="DHCP PC Kantor Pelabuhan" src="https://github.com/user-attachments/assets/10a646aa-97e7-4696-a9c5-1a33f38163bf" /> |

Bukti CCTV mendapat IP dari SRV-DC (bukan router):

| Pengujian | Hasil | Bukti |
|---|---|---|
| Pool CCTV-KOTA di SRV-DC | start 10.96.96.2, gateway 10.96.96.1, DNS 10.96.239.10, 6000 user | <img width="2048" height="704" alt="image" src="https://github.com/user-attachments/assets/cffdfb3c-4da7-41b9-918d-9acbe71abd39" />|
| `ipconfig /all` PC-CCTV-KOTA | DHCP Servers: **10.96.239.10** | <img width="585" height="469" alt="image" src="https://github.com/user-attachments/assets/31b8cbac-eafc-4fe2-bc79-85b7e9a85b5a" />|

## 6. NAT 

**NAT** (*Network Address Translation*) adalah proses mengganti alamat IP sumber pada paket saat melewati router. Alamat privat 10.96.x.x tidak dapat dirutekan di internet, sehingga sebelum keluar ke R-ISP alamat tersebut diterjemahkan menjadi alamat publik milik R-PUSAT.

| Istilah | Penjelasan |
|---|---|
| **Inside** | Sisi jaringan internal (alamat privat). Di R-PUSAT: semua interface LAN dan backbone. |
| **Outside** | Sisi jaringan luar (internet). Di R-PUSAT: Gi0/0 yang menuju R-ISP. |
| **PAT / NAT overload** | Banyak host privat memakai **satu** IP publik yang sama, dibedakan melalui nomor port. |
| **ACL (access-list)** | Daftar alamat yang boleh diterjemahkan oleh NAT. |

Penerapan sesuai ketentuan soal:
- **Seluruh host** (10.96.0.0/16) dapat mengakses server 8.8.8.8.
- Semua host keluar memakai **satu IP publik milik R-PUSAT**: **203.0.113.49** (Gi0/0).
- **R-ISP tidak memiliki rute ke alamat privat**. R-ISP hanya mengenal jaringan yang terhubung langsung: 203.0.113.48/30 dan 8.8.8.0/24.

### 6.1 Konfigurasi di R-PUSAT
```
enable
configure terminal
access-list 1 permit 10.96.0.0 0.0.255.255
ip nat inside source list 1 interface GigabitEthernet0/0 overload
interface GigabitEthernet0/0
 ip nat outside
interface GigabitEthernet0/1
 ip nat inside
interface GigabitEthernet0/2
 ip nat inside
interface Serial0/0/0
 ip nat inside
interface Serial0/0/1
 ip nat inside
interface Serial0/1/0
 ip nat inside
interface Serial0/1/1
 ip nat inside
interface Serial0/2/0
 ip nat inside
end
write memory
```

| Perintah | Fungsi |
|---|---|
| `access-list 1 permit 10.96.0.0 0.0.255.255` | Menentukan alamat yang diterjemahkan: seluruh blok kelompok 10.96.0.0/16 (wildcard 0.0.255.255 = kebalikan dari /16) |
| `ip nat inside source list 1 interface Gi0/0 overload` | Alamat yang cocok dengan ACL 1 diterjemahkan ke IP interface Gi0/0 (203.0.113.49), dipakai bersama (*overload*) |
| `ip nat outside` | Menandai Gi0/0 sebagai sisi internet |
| `ip nat inside` | Menandai interface LAN dan backbone sebagai sisi internal |

R-ISP tidak diberi static route sama sekali. Default route R-PUSAT (`ip route 0.0.0.0 0.0.0.0 203.0.113.50`) mengarahkan semua lalu lintas internet ke R-ISP.

### 6.2 Cara kerja
1. PC-PEGAWAI-BALAIKOTA (10.96.224.2) melakukan ping ke 8.8.8.8.
2. Paket sampai di R-PUSAT, lalu keluar lewat Gi0/0. NAT mengganti alamat sumber **10.96.224.2 → 203.0.113.49**.
3. R-ISP meneruskan paket ke SRV-INTERNET (8.8.8.8). Balasannya ditujukan ke **203.0.113.49**, alamat yang dikenal R-ISP karena terhubung langsung.
4. R-PUSAT mencocokkan balasan dengan tabel translasi NAT, lalu mengembalikan alamat tujuan **203.0.113.49 → 10.96.224.2**.

Karena R-ISP hanya pernah melihat alamat 203.0.113.49, R-ISP tidak memerlukan rute ke alamat privat 10.96.x.x.

### 6.3 Bukti pengujian

**a. Akses ke 8.8.8.8 dari berbagai wilayah**

| No | Perangkat | Wilayah | Perintah | Bukti |
|---|---|---|---|---|
| 1 | PC-PEGAWAI-BALAIKOTA | Balai Kota (R-PUSAT) | `ping 8.8.8.8` | <img width="760" height="261" alt="image" src="https://github.com/user-attachments/assets/666cd8e2-1547-4491-a898-f4c6f8cd6ff2" />|
| 2 | PC-CCTV-KOTA | DC (R-DC) | `ping 8.8.8.8` | <img width="822" height="289" alt="image" src="https://github.com/user-attachments/assets/17e003c7-c3d7-4411-a4e1-9f500dfedb8a" />|
| 3 | LAPTOP-WIFI-UTARA | Utara (R-UTARA) | `ping 8.8.8.8` | <img width="703" height="262" alt="image" src="https://github.com/user-attachments/assets/1da4986e-1afa-49ee-b0b9-93e042692caf" />|
| 4 | SP-WIFI-SELATAN | Selatan (R-SELATAN) | `ping 8.8.8.8` | <img width="727" height="277" alt="image" src="https://github.com/user-attachments/assets/088bc009-60d4-4c52-b4d9-dc203b079816" />|

**b. Translasi NAT dan tabel routing R-ISP**

| Pengujian | Hasil | Bukti |
|---|---|---|
| `show ip nat translations` di R-PUSAT | Alamat privat dari blok 10.96.0.0/16 (inside local) diterjemahkan ke satu alamat publik **203.0.113.49** (inside global) | <img width="350" alt="NAT translations R-PUSAT" src="https://github.com/user-attachments/assets/e035aa69-d8fa-4e93-bcfc-c58ed0a63645" /> |
| `show ip route` di R-ISP | Hanya rute connected 203.0.113.48/30 dan 8.8.8.0/24, **tidak ada rute ke 10.96.0.0/16** |<img width="882" height="352" alt="image" src="https://github.com/user-attachments/assets/bab2529e-cacd-4fd0-b55b-81045c54e70f" />|

Seluruh host dapat mengakses 8.8.8.8 menggunakan satu IP publik R-PUSAT (203.0.113.49), dan R-ISP tidak memiliki rute ke alamat privat, sesuai ketentuan soal 5.

## 7. Tunnel GRE 

**GRE** (*Generic Routing Encapsulation*) adalah protokol yang membungkus (*enkapsulasi*) sebuah paket IP ke dalam paket IP baru, sehingga dua router dapat terhubung seolah-olah langsung bertetangga melalui **tunnel**, meskipun secara fisik paket tetap melewati router lain.

| Istilah | Penjelasan |
|---|---|
| **Tunnel interface** | Interface virtual (Tunnel0) yang menjadi ujung tunnel di setiap router. |
| **Tunnel source** | Interface fisik/alamat asal paket GRE (R-PUSAT Serial0/0/1, R-PELABUHAN Serial0/0/0). |
| **Tunnel destination** | Alamat router di ujung lain tunnel. |
| **Enkapsulasi** | Paket asli diberi header GRE dan header IP baru sebelum dikirim melalui jalur fisik. |

Tunnel dibangun antara **R-PUSAT** dan **R-PELABUHAN** dengan alamat 10.96.241.60/30 (sesuai tabel VLSM). Secara fisik paket tetap melewati R-PUSAT → L2 → R-UTARA → L6 → R-PELABUHAN, tetapi R-UTARA hanya melihat paket GRE antara 10.96.241.37 dan 10.96.241.54, bukan alamat asli Balai Kota atau Pelabuhan.

### 7.1 Konfigurasi

**R-PUSAT**
```
interface Tunnel0
 ip address 10.96.241.61 255.255.255.252
 tunnel source Serial0/0/1
 tunnel destination 10.96.241.54
 tunnel mode gre ip
exit
ip route 10.96.160.0 255.255.224.0 10.96.241.62
ip route 10.96.240.0 255.255.255.128 10.96.241.62
ip route 10.96.160.0 255.255.224.0 10.96.241.38 5
ip route 10.96.240.0 255.255.255.128 10.96.241.38 5
```

**R-PELABUHAN**
```
interface Tunnel0
 ip address 10.96.241.62 255.255.255.252
 tunnel source Serial0/0/0
 tunnel destination 10.96.241.37
 tunnel mode gre ip
exit
ip route 10.96.224.0 255.255.248.0 10.96.241.61
ip route 10.96.241.0 255.255.255.224 10.96.241.61
```

| Router | Tujuan | Next-hop | AD | Keterangan |
|---|---|---|---|---|
| R-PUSAT | 10.96.160.0/19 (SCADA Pelabuhan) | 10.96.241.62 | 1 | Rute utama lewat tunnel |
| R-PUSAT | 10.96.240.0/25 (Kantor Pelabuhan) | 10.96.241.62 | 1 | Rute utama lewat tunnel |
| R-PUSAT | 10.96.160.0/19 dan 10.96.240.0/25 | 10.96.241.38 | 5 | Rute cadangan lewat R-UTARA (*floating static route*) |
| R-PELABUHAN | 10.96.224.0/21 (Pegawai Balai Kota) | 10.96.241.61 | 1 | Lewat tunnel |
| R-PELABUHAN | 10.96.241.0/27 (Server Balai Kota) | 10.96.241.61 | 1 | Lewat tunnel |

### 7.2 Mengapa router memilih jalur tunnel?

- **Di R-PUSAT** terdapat dua rute dengan prefix yang **sama persis** menuju LAN Pelabuhan: melalui tunnel (AD 1) dan melalui R-UTARA (AD 5). Jika prefix sama, router memilih rute dengan **Administrative Distance (AD) terkecil**, sehingga rute tunnel yang dipasang di tabel routing. Rute AD 5 hanya aktif bila tunnel mati.
- **Di R-PELABUHAN** rute ke LAN Balai Kota (/21 dan /27) **lebih spesifik** daripada default route 0.0.0.0/0 ke R-UTARA. Router selalu memilih prefix terpanjang (**longest prefix match**), sehingga balasan juga masuk tunnel.
- Rute tunnel hanya mencakup LAN Balai Kota dan LAN Pelabuhan. Lalu lintas Pelabuhan ke wilayah lain tetap memakai default route biasa.
- Alamat tujuan tunnel (10.96.241.54 dan 10.96.241.37) tidak termasuk rute tunnel, sehingga tidak terjadi *recursive routing* (tunnel mencoba merutekan dirinya sendiri lewat dirinya sendiri).

### 7.3 Mengapa GRE saja tidak mengenkripsi data?

GRE hanya protokol **enkapsulasi**: ia menambahkan header GRE (4 byte) dan header IP baru (20 byte) agar paket dapat dikirim melalui tunnel. GRE **tidak memiliki mekanisme enkripsi, autentikasi, maupun pemeriksaan integritas**, sehingga isi paket asli (alamat IP dan data) tetap dapat dibaca oleh siapa pun yang menyadap jalur di antaranya, misalnya di R-UTARA. Untuk mengamankan isi tunnel diperlukan protokol tambahan seperti **IPsec**.

### 7.4 Bukti pengujian

| No | Pengujian | Hasil | Bukti |
|---|---|---|---|
| 1 | `show interface Tunnel0` di R-PUSAT | Tunnel0 is up, line protocol is up; IP 10.96.241.61/30; source 10.96.241.37 (Serial0/0/1), destination 10.96.241.54; GRE/IP | <img width="751" height="463" alt="image" src="https://github.com/user-attachments/assets/8b766beb-a9d1-438e-9e11-bb6cb16c4ab6" />|
| 2 | `show ip route 10.96.224.0` dan `ping 10.96.241.61` di R-PELABUHAN | Rute ke LAN Pegawai Balai Kota via 10.96.241.61 (tunnel, distance 1); ping ke ujung tunnel 5/5 berhasil | <img width="838" height="259" alt="image" src="https://github.com/user-attachments/assets/a40ac2c0-f647-4331-999c-2736111a2f98" />|
| 3 | `tracert 10.96.240.2` dari PC-PEGAWAI-BALAIKOTA | 10.96.224.1 → **10.96.241.62** → 10.96.240.2 | <img width="612" height="186" alt="image" src="https://github.com/user-attachments/assets/b497c5d2-8ba7-4f43-a87e-3933b8b008f4" />|
| 4 | `tracert 10.96.224.2` dari PC-KANTOR-PELABUHAN | 10.96.240.1 → **10.96.241.61** → 10.96.224.2 | <img width="603" height="184" alt="image" src="https://github.com/user-attachments/assets/16421794-b31f-4df1-be87-1d9bbcc2248d" />|
| 5 | `tracert 10.96.239.10` dari PC-KANTOR-PELABUHAN (pembanding) | 10.96.240.1 → 10.96.241.53 → 10.96.241.37 → 10.96.241.34 → 10.96.239.10 | <img width="646" height="181" alt="image" src="https://github.com/user-attachments/assets/a1b1b505-eb5d-45b9-8555-da16e2fe797c" />|

**Analisis:** Pada pengujian 3 dan 4, hop kedua adalah alamat Tunnel0 (10.96.241.62 dan 10.96.241.61), dan tracert hanya menampilkan 3 hop. Padahal jalur fisiknya melewati R-UTARA (4 hop). R-UTARA tidak terlihat karena hanya meneruskan paket GRE 10.96.241.37 ↔ 10.96.241.54 tanpa memproses paket aslinya, sehingga R-PUSAT dan R-PELABUHAN tampak bertetangga langsung. Sebaliknya, pada pengujian 5 R-UTARA (10.96.241.53) muncul karena lalu lintas Pelabuhan ke Data Center tidak termasuk rute tunnel.

Hop kedua pada pengujian 3 dan 4 adalah alamat tunnel, membuktikan lalu lintas Balai Kota ↔ Pelabuhan melewati tunnel GRE. Lalu lintas Pelabuhan ke wilayah lain (pengujian 5) tetap melalui jalur fisik R-UTARA.
