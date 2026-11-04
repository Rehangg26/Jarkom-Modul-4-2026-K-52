# Perhitungan VLSM CPT

Kebutuhan IP mencakup host dan interface router pada segmen. Pilih blok terkecil yang memenuhi `2^h - 2 >= kebutuhan IP`, kemudian prefix = `32 - h`. Alokasi dilakukan dari blok terbesar. A19 ditempatkan sebelum subnet /30 karena membutuhkan /29.

Contoh A1: 700 + 180 + 1 = 881 IP usable; /23 hanya 510, sehingga digunakan /22 (1022 usable). A5: 208 + 45 + 1 = 254, tepat menggunakan /24. A19 mencakup empat interface router, sehingga /30 tidak cukup dan digunakan /29.

| Subnet | Segmen | Kebutuhan IP | Network | Netmask | Broadcast | IP usable |
|---|---|---:|---|---|---|---|
| A1 | Psyche → Switch3 → Karl, Lin | 881 | 192.237.0.0/22 | 255.255.252.0 | 192.237.3.255 | 192.237.0.1 – 192.237.3.254 |
| A2 | Accela → Switch5 → Omnipresence, Denpa | 514 | 192.237.4.0/22 | 255.255.252.0 | 192.237.7.255 | 192.237.4.1 – 192.237.7.254 |
| A3 | Copland → Switch4 → Masayuki | 301 | 192.237.8.0/23 | 255.255.254.0 | 192.237.9.255 | 192.237.8.1 – 192.237.9.254 |
| A4 | Tachibana → Switch9 → Collective, Ghost | 267 | 192.237.10.0/23 | 255.255.254.0 | 192.237.11.255 | 192.237.10.1 – 192.237.11.254 |
| A5 | Navi → Switch6 → Alice dan Reika | 254 | 192.237.12.0/24 | 255.255.255.0 | 192.237.12.255 | 192.237.12.1 – 192.237.12.254 |
| A6 | Navi → Switch8 → Yasuo, Miho, Kuma | 143 | 192.237.13.0/24 | 255.255.255.0 | 192.237.13.255 | 192.237.13.1 – 192.237.13.254 |
| A7 | Cyberia → Switch1 → JJ, MyuMyu | 126 | 192.237.14.0/25 | 255.255.255.128 | 192.237.14.127 | 192.237.14.1 – 192.237.14.126 |
| A8 | Deus → Switch10 → Lilith dan Specter | 99 | 192.237.14.128/25 | 255.255.255.128 | 192.237.14.255 | 192.237.14.129 – 192.237.14.254 |
| A9 | Shibuya → Switch13 → Lain, Mika | 62 | 192.237.15.0/26 | 255.255.255.192 | 192.237.15.63 | 192.237.15.1 – 192.237.15.62 |
| A10 | Navi → Switch7 → Taro | 58 | 192.237.15.64/26 | 255.255.255.192 | 192.237.15.127 | 192.237.15.65 – 192.237.15.126 |
| A11 | Protocol7 → Switch11 → Chisa, Hodgeson | 33 | 192.237.15.128/26 | 255.255.255.192 | 192.237.15.191 | 192.237.15.129 – 192.237.15.190 |
| A12 | Copland → Juri | 32 | 192.237.15.192/26 | 255.255.255.192 | 192.237.15.255 | 192.237.15.193 – 192.237.15.254 |
| A13 | Phantoma → Switch12 → Semi | 15 | 192.237.16.0/27 | 255.255.255.224 | 192.237.16.31 | 192.237.16.1 – 192.237.16.30 |
| A14 | Schumann → Akashic | 4 | 192.237.16.32/29 | 255.255.255.248 | 192.237.16.39 | 192.237.16.33 – 192.237.16.38 |
| A15 | Knights → Eiri | 3 | 192.237.16.40/29 | 255.255.255.248 | 192.237.16.47 | 192.237.16.41 – 192.237.16.46 |
| A19 | Wired → Switch2 → Psyche, Copland, Schumann | 4 | 192.237.16.48/29 | 255.255.255.248 | 192.237.16.55 | 192.237.16.49 – 192.237.16.54 |
| A16 | Cyberia → Wired | 2 | 192.237.16.56/30 | 255.255.255.252 | 192.237.16.59 | 192.237.16.57 – 192.237.16.58 |
| A17 | Wired → Navi | 2 | 192.237.16.60/30 | 255.255.255.252 | 192.237.16.63 | 192.237.16.61 – 192.237.16.62 |
| A18 | Wired → Tachibana | 2 | 192.237.16.64/30 | 255.255.255.252 | 192.237.16.67 | 192.237.16.65 – 192.237.16.66 |
| A20 | Schumann → Accela | 2 | 192.237.16.68/30 | 255.255.255.252 | 192.237.16.71 | 192.237.16.69 – 192.237.16.70 |
| A21 | Tachibana → Deus | 2 | 192.237.16.72/30 | 255.255.255.252 | 192.237.16.75 | 192.237.16.73 – 192.237.16.74 |
| A22 | Deus → Protocol7 | 2 | 192.237.16.76/30 | 255.255.255.252 | 192.237.16.79 | 192.237.16.77 – 192.237.16.78 |
| A23 | Protocol7 → Knights | 2 | 192.237.16.80/30 | 255.255.255.252 | 192.237.16.83 | 192.237.16.81 – 192.237.16.82 |
| A24 | Knights → Phantoma | 2 | 192.237.16.84/30 | 255.255.255.252 | 192.237.16.87 | 192.237.16.85 – 192.237.16.86 |
| A25 | Phantoma → Shibuya | 2 | 192.237.16.88/30 | 255.255.255.252 | 192.237.16.91 | 192.237.16.89 – 192.237.16.90 |

Total kebutuhan: **2814 IP usable**. Total blok dialokasikan: **4188 alamat**, termasuk network dan broadcast. Blok induk terkecil yang mencakup seluruh alokasi adalah **192.237.0.0/19**.

