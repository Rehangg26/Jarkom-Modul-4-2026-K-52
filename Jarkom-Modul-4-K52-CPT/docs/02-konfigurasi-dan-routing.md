# Implementasi dan routing CPT

## Urutan pengerjaan

1. Identifikasi segmen LAN dan transit pada topologi, lalu hitung host beserta interface router.
2. Tentukan prefix minimum dan alokasikan alamat VLSM dari blok terbesar. Hasil akhir ada pada tabel VLSM.
3. Pasang IP dan subnet mask pada host, isi default gateway ke interface router pada LAN yang sama.
4. Pasang IP pada interface router dan aktifkan interface dengan `no shutdown`. Cocokkan port aktual melalui `show ip interface brief`.
5. Pasang static route menuju subnet yang tidak langsung terhubung. Router ujung dapat menggunakan default route ke router hulu.
6. Periksa route yang belum ada, tambahkan route pada seluruh router perantara, lalu uji ping beserta jalur balik.
7. Simpan running-config ke startup-config pada semua router, simpan .pkt, dan uji pemuatan ulang Shibuya.

Langkah 1–5 merangkum metode implementasi. Rincian perintah yang dapat ditelusuri dari percakapan tersedia di bawah; berkas ini bukan salinan lengkap running-config seluruh router.

## Rute transit yang ditambahkan dan diperiksa

| Router | Network tujuan | Mask | Next hop |
|---|---|---|---|
| Wired | 192.237.16.84 | 255.255.255.252 | 192.237.16.66 |
| Tachibana | 192.237.16.84 | 255.255.255.252 | 192.237.16.74 |
| Deus | 192.237.16.84 | 255.255.255.252 | 192.237.16.78 |
| Protocol7 | 192.237.16.84 | 255.255.255.252 | 192.237.16.82 |
| Wired | 192.237.16.80 | 255.255.255.252 | 192.237.16.66 |
| Tachibana | 192.237.16.80 | 255.255.255.252 | 192.237.16.74 |
| Deus | 192.237.16.80 | 255.255.255.252 | 192.237.16.78 |
| Wired | 192.237.16.76 | 255.255.255.252 | 192.237.16.66 |
| Tachibana | 192.237.16.76 | 255.255.255.252 | 192.237.16.74 |
| Wired | 192.237.16.72 | 255.255.255.252 | 192.237.16.66 |
| Wired | 192.237.16.68 | 255.255.255.252 | 192.237.16.52 |

Format pemasangan setiap baris:

```text
configure terminal
ip route <network-tujuan> <subnet-mask> <next-hop>
end
show ip route <ip-tujuan>
```

Route yang muncul pada tabel routing membuktikan pemasangan route, sedangkan ping menguji keterjangkauan tujuan dan balasannya.

## Perbaikan salah router

Route A22 melalui 192.237.16.74 sempat dimasukkan ke Wired. Entri tersebut dihapus secara spesifik agar route yang benar melalui Tachibana tetap ada:

```text
configure terminal
no ip route 192.237.16.76 255.255.255.252 192.237.16.74
end
show ip route 192.237.16.78
```

Hasil pemeriksaan Wired hanya menunjukkan next hop 192.237.16.66. Route melalui .74 kemudian dipasang pada Tachibana.

## Penyimpanan dan uji restart

Perintah berikut digunakan pada Wired, Tachibana, Deus, Protocol7, Knights, Phantoma, Shibuya, Cyberia, Navi, Psyche, Copland, Schumann, dan Accela:

```text
copy running-config startup-config
```

Penyimpanan Wired dibuktikan output `[OK]`; penyimpanan router lain dikonfirmasi pengguna. File Packet Tracer disimpan dengan Ctrl+S.

Shibuya kemudian menjalankan `reload`. Setelah menyala, output menunjukkan:

```text
FastEthernet0/0  192.237.16.90  YES NVRAM  up  up
FastEthernet1/0  192.237.15.1   YES NVRAM  up  up
C  192.237.15.0/26 is directly connected, FastEthernet1/0
C  192.237.16.88/30 is directly connected, FastEthernet0/0
S* 0.0.0.0/0 [1/0] via 192.237.16.89
```

Ping Lain ke Karl 192.237.0.2 sesudah restart mendapat 4/4 balasan. Persistensi konfigurasi diuji langsung pada Shibuya; router lain tidak diklaim telah diuji restart.

## Catatan efisiensi

Prefix pada seluruh baris VLSM cukup dan merupakan blok minimum untuk kebutuhan masing-masing. Alokasi tidak overlap. Efisiensi seluruh static route belum dapat diaudit tanpa running-config semua router. Ringkasan /19 pada Cyberia disebut dalam proses penyimpanan, tetapi baris konfigurasi lengkapnya tidak tersedia dalam bukti yang disertakan.
