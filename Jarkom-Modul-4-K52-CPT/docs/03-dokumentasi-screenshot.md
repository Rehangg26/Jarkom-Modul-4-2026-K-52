# Dokumentasi screenshot pengerjaan CPT

Seluruh gambar merupakan screenshot asli kiriman praktikan. Urutan berikut mengikuti tahapan konfigurasi, bukan waktu pembuatan file. Gambar yang memperlihatkan status down merupakan bukti tahap pemasangan, bukan bukti hasil akhir.

## LAN A1: Psyche, Karl, dan Lin

### Bukti 01 — Di Router Psyche

Psyche Fa0/1 diberi 192.237.0.1/22 dan diaktifkan dengan no shutdown.

![Bukti 01: Di Router Psyche](../bukti/screenshot/01-di-router-psyche.png)

### Bukti 02 — Atur IP di Karl

Karl memakai 192.237.0.2 dan gateway 192.237.0.1. Screenshot menunjukkan mask /24; rencana A1 menggunakan /22. Ini merupakan ketidaksesuaian yang harus diperiksa pada project akhir.

![Bukti 02: Atur IP di Karl](../bukti/screenshot/02-atur-ip-di-karl.png)

### Bukti 03 — IP di Lin

Lin memakai 192.237.0.3/22 dengan gateway 192.237.0.1.

![Bukti 03: IP di Lin](../bukti/screenshot/03-ip-di-lin.png)

### Bukti 04 — Test Ping dari Lin ke Karl

Lin menguji 192.237.0.2: empat balasan, 0% loss. Ping lokal ini tidak membuktikan mask Karl sudah /22.

![Bukti 04: Test Ping dari Lin ke Karl](../bukti/screenshot/04-test-ping-dari-lin-ke-karl.png)

## Segmen bersama A19 dan routing A1

### Bukti 05 — FastEthernet 11 Wired ke switch 2

Interface Wired menuju Switch2 memakai 192.237.16.49/29.

![Bukti 05: FastEthernet 11 Wired ke switch 2](../bukti/screenshot/05-fastethernet-11-wired-ke-switch-2.png)

### Bukti 06 — konfigurasi FastEthernet 00 Psyche

Psyche Fa0/0 memakai 192.237.16.50/29.

![Bukti 06: konfigurasi FastEthernet 00 Psyche](../bukti/screenshot/06-konfigurasi-fastethernet-00-psyche.png)

### Bukti 07 — aktifkan Fa00 Psyche untuk subnet A19

CLI mengonfirmasi Fa0/0 Psyche aktif up/up dengan 192.237.16.50.

![Bukti 07: aktifkan Fa00 Psyche untuk subnet A19](../bukti/screenshot/07-aktifkan-fa00-psyche-untuk-subnet-a19.png)

### Bukti 08 — tambahkan rute ke LAN A1 di Wired

Wired memasang rute 192.237.0.0/22 melalui 192.237.16.50. Pesan invalid input berasal dari configure terminal yang dijalankan saat sudah berada pada mode konfigurasi; perintah ip route berikutnya berhasil.

![Bukti 08: tambahkan rute ke LAN A1 di Wired](../bukti/screenshot/08-tambahkan-rute-ke-lan-a1-di-wired.png)

### Bukti 09 — konfigurasi Fa00 Copland yang menuju Switch2

Copland Fa0/0 memakai 192.237.16.51/29.

![Bukti 09: konfigurasi Fa00 Copland yang menuju Switch2](../bukti/screenshot/09-konfigurasi-fa00-copland-yang-menuju-switch2.png)

### Bukti 10 — Konfigurasi Fa00 Schumann yang menuju Switch2

Schumann Fa0/0 memakai 192.237.16.52/29 dan aktif up/up.

![Bukti 10: Konfigurasi Fa00 Schumann yang menuju Switch2](../bukti/screenshot/10-konfigurasi-fa00-schumann-yang-menuju-switch2.png)

### Bukti 11 — Bukti Schuman bisa menjangkau Wired

Schumann melakukan ping 192.237.16.49 dengan hasil 5/5, RTT 0/2/13 ms.

![Bukti 11: Bukti Schuman bisa menjangkau Wired](../bukti/screenshot/11-bukti-schuman-bisa-menjangkau-wired.png)

## Copland: LAN A3 dan A12

### Bukti 12 — Atur Fa10 Copland sebagai gateway LAN Masayuki (A3)

Copland Fa1/0 memakai 192.237.8.1/23 dan aktif up/up.

![Bukti 12: Atur Fa10 Copland sebagai gateway LAN Masayuki (A3)](../bukti/screenshot/12-atur-fa10-copland-sebagai-gateway-lan-masayuki-a3.png)

### Bukti 13 — Mengatur IP Masayuki

Masayuki memakai 192.237.8.2/23 dan gateway 192.237.8.1.

![Bukti 13: Mengatur IP Masayuki](../bukti/screenshot/13-mengatur-ip-masayuki.png)

### Bukti 14 — Ping Masayuki ke gateaway A3

Masayuki berhasil ping gateway 192.237.8.1, empat balasan tanpa loss.

![Bukti 14: Ping Masayuki ke gateaway A3](../bukti/screenshot/14-ping-masayuki-ke-gateaway-a3.png)

### Bukti 15 — Menambahkan rute ke A3 dari Wired

Wired memasang 192.237.8.0/23 melalui Copland 192.237.16.51.

![Bukti 15: Menambahkan rute ke A3 dari Wired](../bukti/screenshot/15-menambahkan-rute-ke-a3-dari-wired.png)

### Bukti 16 — Konfigurasikan Fa01 Copland sebagai gateway Juri di subnet A12

Copland Fa0/1 memakai 192.237.15.193/26 dan aktif up/up.

![Bukti 16: Konfigurasikan Fa01 Copland sebagai gateway Juri di subnet A12](../bukti/screenshot/16-konfigurasikan-fa01-copland-sebagai-gateway-juri-di-subnet-a12.png)

### Bukti 17 — Mengatur IP Juri

Juri memakai 192.237.15.194/26 dan gateway 192.237.15.193.

![Bukti 17: Mengatur IP Juri](../bukti/screenshot/17-mengatur-ip-juri.png)

### Bukti 18 — Cek ping Juri ke gateaway Copland

Juri berhasil ping 192.237.15.193 dengan empat balasan tanpa loss, RTT min/max/avg 0/9/2 ms.

![Bukti 18: Cek ping Juri ke gateaway Copland](../bukti/screenshot/18-cek-ping-juri-ke-gateaway-copland.png)

## Schumann: LAN Akashic dan jalur keluar cabang

### Bukti 19 — mengatur Fa01 Schumann sebagai gateway Akashic di subnet A14

Schumann Fa0/1 memakai 192.237.16.33/29.

![Bukti 19: mengatur Fa01 Schumann sebagai gateway Akashic di subnet A14](../bukti/screenshot/19-mengatur-fa01-schumann-sebagai-gateway-akashic-di-subnet-a14.png)

### Bukti 20 — IP Akashic

Akashic memakai 192.237.16.34/29 dan gateway 192.237.16.33.

![Bukti 20: IP Akashic](../bukti/screenshot/20-ip-akashic.png)

### Bukti 21 — menambahkan rute default di Psyche

Tabel Psyche menunjukkan default route melalui Wired 192.237.16.49.

![Bukti 21: menambahkan rute default di Psyche](../bukti/screenshot/21-menambahkan-rute-default-di-psyche.png)

### Bukti 22 — menambahkan rute default di Copland

Copland memasang default route melalui Wired 192.237.16.49.

![Bukti 22: menambahkan rute default di Copland](../bukti/screenshot/22-menambahkan-rute-default-di-copland.png)

### Bukti 23 — Ping dari Akashic ke Karl

Akashic berhasil ping Karl 192.237.0.2 dengan empat balasan, 0% loss dan TTL 125.

![Bukti 23: Ping dari Akashic ke Karl](../bukti/screenshot/23-ping-dari-akashic-ke-karl.png)

## Cyberia dan link A16

### Bukti 24 — mengatur Fa00 Wired untuk link ke Cyberia (A16)

Wired Fa0/0 diberi 192.237.16.57/30. Pada tahap ini link masih down karena sisi lawan belum aktif.

![Bukti 24: mengatur Fa00 Wired untuk link ke Cyberia (A16)](../bukti/screenshot/24-mengatur-fa00-wired-untuk-link-ke-cyberia-a16.png)

### Bukti 25 — Mengatur Fa10 Cyberia untuk link A16 ke Wired

Cyberia Fa1/0 diberi 192.237.16.58/30.

![Bukti 25: Mengatur Fa10 Cyberia untuk link A16 ke Wired](../bukti/screenshot/25-mengatur-fa10-cyberia-untuk-link-a16-ke-wired.png)

### Bukti 26 — uji link A16 dari Cyberia ke Wired

Cyberia ping Wired 192.237.16.57, berhasil 5/5 dengan RTT 0/0/0 ms.

![Bukti 26: uji link A16 dari Cyberia ke Wired](../bukti/screenshot/26-uji-link-a16-dari-cyberia-ke-wired.png)

## Host cabang Navi

### Bukti 27 — IP Alice

Alice memakai 192.237.12.2/24 dan gateway 192.237.12.1 pada A5.

![Bukti 27: IP Alice](../bukti/screenshot/27-ip-alice.png)

### Bukti 28 — IP Kuma

Kuma memakai 192.237.13.4/24 dan gateway 192.237.13.1 pada A6.

![Bukti 28: IP Kuma](../bukti/screenshot/28-ip-kuma.png)

### Bukti 29 — IP Taro

Taro memakai 192.237.15.66/26 dan gateway 192.237.15.65 pada A10.

![Bukti 29: IP Taro](../bukti/screenshot/29-ip-taro.png)

## Tachibana dan Deus

### Bukti 30 — mengatur Fa10 Wired untuk link ke Tachibana (A18)

Wired Fa1/0 memakai 192.237.16.65 pada link A18.

![Bukti 30: mengatur Fa10 Wired untuk link ke Tachibana (A18)](../bukti/screenshot/30-mengatur-fa10-wired-untuk-link-ke-tachibana-a18.png)

### Bukti 31 — Atur Fa00 Tachibana untuk link A18 ke Wired

Tachibana Fa0/0 memakai 192.237.16.66 dan aktif up/up.

![Bukti 31: Atur Fa00 Tachibana untuk link A18 ke Wired](../bukti/screenshot/31-atur-fa00-tachibana-untuk-link-a18-ke-wired.png)

### Bukti 32 — IP Collective

Collective memakai 192.237.10.2/23 dan gateway 192.237.10.1 pada A4.

![Bukti 32: IP Collective](../bukti/screenshot/32-ip-collective.png)

### Bukti 33 — Menyambungkan Tachiban dengan Deus

Tachibana Fa0/1 diberi 192.237.16.73/30 untuk A21. Status down pada screenshot merupakan keadaan sebelum sisi Deus aktif.

![Bukti 33: Menyambungkan Tachiban dengan Deus](../bukti/screenshot/33-menyambungkan-tachiban-dengan-deus.png)

### Bukti 34 — IP Lilith

Lilith memakai 192.237.14.130/25 dan gateway 192.237.14.129 pada A8.

![Bukti 34: IP Lilith](../bukti/screenshot/34-ip-lilith.png)

## Host Protocol7 sampai Shibuya

### Bukti 35 — IP Chisa

Chisa memakai 192.237.15.130/26 dan gateway 192.237.15.129 pada A11.

![Bukti 35: IP Chisa](../bukti/screenshot/35-ip-chisa.png)

### Bukti 36 — IP Eiri

Eiri memakai 192.237.16.42/29 dan gateway 192.237.16.41 pada A15.

![Bukti 36: IP Eiri](../bukti/screenshot/36-ip-eiri.png)

### Bukti 37 — IP Semi

Semi memakai 192.237.16.2/27 dan gateway 192.237.16.1 pada A13.

![Bukti 37: IP Semi](../bukti/screenshot/37-ip-semi.png)

### Bukti 38 — IP Lain

Lain memakai 192.237.15.2/26 dan gateway 192.237.15.1 pada A9.

![Bukti 38: IP Lain](../bukti/screenshot/38-ip-lain.png)

