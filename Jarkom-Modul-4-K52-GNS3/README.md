## Router Cyberia

**Langkah 1 – [Edit config]** (klik kanan Cyberia > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Cyberia: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.168.122.50
    netmask 255.255.255.0
    gateway 192.168.122.1
    up sysctl -w net.ipv4.ip_forward=1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf

auto eth1
iface eth1 inet static
    address 192.237.28.1
    netmask 255.255.255.128

auto eth2
iface eth2 inet static
    address 192.237.28.145
    netmask 255.255.255.252
    up ip route add 192.237.0.0/19 via 192.237.28.146
    up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE || true
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Cyberia.

**Langkah 3 – [Web console]** (console Cyberia; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Cyberia =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.168.122.50/24 dev eth0  # [CONFIG] address + netmask eth0
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.1/25 dev eth1  # [CONFIG] address + netmask eth1 (A7)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth2  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.145/30 dev eth2  # [CONFIG] address + netmask eth2 (A16)
ip link set eth2 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.168.122.1  # [CONFIG] gateway eth0
ip route replace 192.237.0.0/19 via 192.237.28.146  # [CONFIG] up ip route add 192.237.0.0/19 via 192.237.28.146
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
command -v iptables >/dev/null 2>&1 || apk add --no-cache iptables || (apt-get update && apt-get install -y iptables)  # [SCRIPT] pasang iptables bila belum ada (tidak bisa di config, butuh internet)
iptables -t nat -C POSTROUTING -o eth0 -j MASQUERADE 2>/dev/null || iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE  # [CONFIG] up iptables ... MASQUERADE (+cek -C agar tidak dobel)
EOF
ls /root
```

## Router Wired

**Langkah 1 – [Edit config]** (klik kanan Wired > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Wired: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.28.146
    netmask 255.255.255.252
    gateway 192.237.28.145
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.237.28.149
    netmask 255.255.255.252
    up ip route add 192.237.24.0/22 via 192.237.28.150

auto eth2
iface eth2 inet static
    address 192.237.28.137
    netmask 255.255.255.252
    up ip route add 192.237.16.0/21 via 192.237.28.138

auto eth3
iface eth3 inet static
    address 192.237.28.129
    netmask 255.255.255.248
    up ip route add 192.237.0.0/22 via 192.237.28.130
    up ip route add 192.237.4.0/22 via 192.237.28.131
    up ip route add 192.237.8.0/21 via 192.237.28.132
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Wired.

**Langkah 3 – [Web console]** (console Wired; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Wired =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.146/30 dev eth0  # [CONFIG] address + netmask eth0 (A16)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.149/30 dev eth1  # [CONFIG] address + netmask eth1 (A17)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth2  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.137/30 dev eth2  # [CONFIG] address + netmask eth2 (A18)
ip link set eth2 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth3  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.129/29 dev eth3  # [CONFIG] address + netmask eth3 (A19)
ip link set eth3 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.28.145  # [CONFIG] gateway
ip route replace 192.237.24.0/22 via 192.237.28.150  # [CONFIG] up ip route add 192.237.24.0/22 via 192.237.28.150
ip route replace 192.237.16.0/21 via 192.237.28.138  # [CONFIG] up ip route add 192.237.16.0/21 via 192.237.28.138
ip route replace 192.237.0.0/22 via 192.237.28.130  # [CONFIG] up ip route add 192.237.0.0/22 via 192.237.28.130
ip route replace 192.237.4.0/22 via 192.237.28.131  # [CONFIG] up ip route add 192.237.4.0/22 via 192.237.28.131
ip route replace 192.237.8.0/21 via 192.237.28.132  # [CONFIG] up ip route add 192.237.8.0/21 via 192.237.28.132
EOF
ls /root
```

## Router Navi

**Langkah 1 – [Edit config]** (klik kanan Navi > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Navi: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.28.150
    netmask 255.255.255.252
    gateway 192.237.28.149
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.237.24.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 192.237.25.1
    netmask 255.255.255.0

auto eth3
iface eth3 inet static
    address 192.237.26.1
    netmask 255.255.255.192
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Navi.

**Langkah 3 – [Web console]** (console Navi; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Navi =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.150/30 dev eth0  # [CONFIG] address + netmask eth0 (A17)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.24.1/24 dev eth1  # [CONFIG] address + netmask eth1 (A5)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth2  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.25.1/24 dev eth2  # [CONFIG] address + netmask eth2 (A6)
ip link set eth2 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth3  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.26.1/26 dev eth3  # [CONFIG] address + netmask eth3 (A10)
ip link set eth3 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.28.149  # [CONFIG] gateway
EOF
ls /root
```

## Router Tachibana

**Langkah 1 – [Edit config]** (klik kanan Tachibana > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Tachibana: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.28.138
    netmask 255.255.255.252
    gateway 192.237.28.137
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.237.16.1
    netmask 255.255.254.0

auto eth2
iface eth2 inet static
    address 192.237.18.1
    netmask 255.255.255.252
    up ip route add 192.237.20.0/22 via 192.237.18.2
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Tachibana.

**Langkah 3 – [Web console]** (console Tachibana; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Tachibana =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.138/30 dev eth0  # [CONFIG] address + netmask eth0 (A18)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.16.1/23 dev eth1  # [CONFIG] address + netmask eth1 (A4)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth2  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.18.1/30 dev eth2  # [CONFIG] address + netmask eth2 (A21)
ip link set eth2 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.28.137  # [CONFIG] gateway
ip route replace 192.237.20.0/22 via 192.237.18.2  # [CONFIG] up ip route add 192.237.20.0/22 via 192.237.18.2
EOF
ls /root
```

## Router Deus

**Langkah 1 – [Edit config]** (klik kanan Deus > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Deus: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.18.2
    netmask 255.255.255.252
    gateway 192.237.18.1
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.237.22.1
    netmask 255.255.255.128

auto eth2
iface eth2 inet static
    address 192.237.22.129
    netmask 255.255.255.252
    up ip route add 192.237.20.0/23 via 192.237.22.130
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Deus.

**Langkah 3 – [Web console]** (console Deus; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Deus =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.18.2/30 dev eth0  # [CONFIG] address + netmask eth0 (A21)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.22.1/25 dev eth1  # [CONFIG] address + netmask eth1 (A8)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth2  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.22.129/30 dev eth2  # [CONFIG] address + netmask eth2 (A22)
ip link set eth2 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.18.1  # [CONFIG] gateway
ip route replace 192.237.20.0/23 via 192.237.22.130  # [CONFIG] up ip route add 192.237.20.0/23 via 192.237.22.130
EOF
ls /root
```

## Router Protocol7

**Langkah 1 – [Edit config]** (klik kanan Protocol7 > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Protocol7: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.22.130
    netmask 255.255.255.252
    gateway 192.237.22.129
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.237.21.1
    netmask 255.255.255.192

auto eth2
iface eth2 inet static
    address 192.237.21.65
    netmask 255.255.255.252
    up ip route add 192.237.20.0/24 via 192.237.21.66
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Protocol7.

**Langkah 3 – [Web console]** (console Protocol7; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Protocol7 =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.22.130/30 dev eth0  # [CONFIG] address + netmask eth0 (A22)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.21.1/26 dev eth1  # [CONFIG] address + netmask eth1 (A11)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth2  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.21.65/30 dev eth2  # [CONFIG] address + netmask eth2 (A23)
ip link set eth2 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.22.129  # [CONFIG] gateway
ip route replace 192.237.20.0/24 via 192.237.21.66  # [CONFIG] up ip route add 192.237.20.0/24 via 192.237.21.66
EOF
ls /root
```

## Router Knights

**Langkah 1 – [Edit config]** (klik kanan Knights > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Knights: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.21.66
    netmask 255.255.255.252
    gateway 192.237.21.65
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.237.20.129
    netmask 255.255.255.248

auto eth2
iface eth2 inet static
    address 192.237.20.137
    netmask 255.255.255.252
    up ip route add 192.237.20.0/25 via 192.237.20.138
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Knights.

**Langkah 3 – [Web console]** (console Knights; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Knights =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.21.66/30 dev eth0  # [CONFIG] address + netmask eth0 (A23)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.20.129/29 dev eth1  # [CONFIG] address + netmask eth1 (A15)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth2  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.20.137/30 dev eth2  # [CONFIG] address + netmask eth2 (A24)
ip link set eth2 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.21.65  # [CONFIG] gateway
ip route replace 192.237.20.0/25 via 192.237.20.138  # [CONFIG] up ip route add 192.237.20.0/25 via 192.237.20.138
EOF
ls /root
```

## Router Phantoma

**Langkah 1 – [Edit config]** (klik kanan Phantoma > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Phantoma: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.20.138
    netmask 255.255.255.252
    gateway 192.237.20.137
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.237.20.1
    netmask 255.255.255.224

auto eth2
iface eth2 inet static
    address 192.237.20.33
    netmask 255.255.255.252
    up ip route add 192.237.20.64/26 via 192.237.20.34
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Phantoma.

**Langkah 3 – [Web console]** (console Phantoma; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Phantoma =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.20.138/30 dev eth0  # [CONFIG] address + netmask eth0 (A24)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.20.1/27 dev eth1  # [CONFIG] address + netmask eth1 (A13)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth2  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.20.33/30 dev eth2  # [CONFIG] address + netmask eth2 (A25)
ip link set eth2 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.20.137  # [CONFIG] gateway
ip route replace 192.237.20.64/26 via 192.237.20.34  # [CONFIG] up ip route add 192.237.20.64/26 via 192.237.20.34
EOF
ls /root
```

## Router Shibuya

**Langkah 1 – [Edit config]** (klik kanan Shibuya > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Shibuya: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.20.34
    netmask 255.255.255.252
    gateway 192.237.20.33
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.237.20.65
    netmask 255.255.255.192
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Shibuya.

**Langkah 3 – [Web console]** (console Shibuya; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Shibuya =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.20.34/30 dev eth0  # [CONFIG] address + netmask eth0 (A25)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.20.65/26 dev eth1  # [CONFIG] address + netmask eth1 (A9)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.20.33  # [CONFIG] gateway
EOF
ls /root
```

## Router Psyche

**Langkah 1 – [Edit config]** (klik kanan Psyche > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Psyche: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.28.130
    netmask 255.255.255.248
    gateway 192.237.28.129
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.237.0.1
    netmask 255.255.252.0
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Psyche.

**Langkah 3 – [Web console]** (console Psyche; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Psyche =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.130/29 dev eth0  # [CONFIG] address + netmask eth0 (A19)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.0.1/22 dev eth1  # [CONFIG] address + netmask eth1 (A1)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.28.129  # [CONFIG] gateway
EOF
ls /root
```

## Router Copland

**Langkah 1 – [Edit config]** (klik kanan Copland > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Copland: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.28.131
    netmask 255.255.255.248
    gateway 192.237.28.129
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.237.4.1
    netmask 255.255.254.0

auto eth2
iface eth2 inet static
    address 192.237.6.1
    netmask 255.255.255.192
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Copland.

**Langkah 3 – [Web console]** (console Copland; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Copland =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.131/29 dev eth0  # [CONFIG] address + netmask eth0 (A19)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.4.1/23 dev eth1  # [CONFIG] address + netmask eth1 (A3)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth2  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.6.1/26 dev eth2  # [CONFIG] address + netmask eth2 (A12)
ip link set eth2 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.28.129  # [CONFIG] gateway
EOF
ls /root
```

## Router Schumann

**Langkah 1 – [Edit config]** (klik kanan Schumann > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Schumann: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.28.132
    netmask 255.255.255.248
    gateway 192.237.28.129
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.237.12.1
    netmask 255.255.255.248

auto eth2
iface eth2 inet static
    address 192.237.12.9
    netmask 255.255.255.252
    up ip route add 192.237.8.0/22 via 192.237.12.10
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Schumann.

**Langkah 3 – [Web console]** (console Schumann; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Schumann =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.132/29 dev eth0  # [CONFIG] address + netmask eth0 (A19)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.12.1/29 dev eth1  # [CONFIG] address + netmask eth1 (A14)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth2  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.12.9/30 dev eth2  # [CONFIG] address + netmask eth2 (A20)
ip link set eth2 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.28.129  # [CONFIG] gateway
ip route replace 192.237.8.0/22 via 192.237.12.10  # [CONFIG] up ip route add 192.237.8.0/22 via 192.237.12.10
EOF
ls /root
```

## Router Accela

**Langkah 1 – [Edit config]** (klik kanan Accela > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Accela: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.12.10
    netmask 255.255.255.252
    gateway 192.237.12.9
    up sysctl -w net.ipv4.ip_forward=1

auto eth1
iface eth1 inet static
    address 192.237.8.1
    netmask 255.255.252.0
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Accela.

**Langkah 3 – [Web console]** (console Accela; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Router Accela =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

echo 1 > /proc/sys/net/ipv4/ip_forward  # [CONFIG] up sysctl -w net.ipv4.ip_forward=1
ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.12.10/30 dev eth0  # [CONFIG] address + netmask eth0 (A20)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip addr flush dev eth1  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.8.1/22 dev eth1  # [CONFIG] address + netmask eth1 (A2)
ip link set eth1 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.12.9  # [CONFIG] gateway
EOF
ls /root
```

## Host Karl

**Langkah 1 – [Edit config]** (klik kanan Karl > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Karl: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.0.2
    netmask 255.255.252.0
    gateway 192.237.0.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Karl.

**Langkah 3 – [Web console]** (console Karl; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Karl =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.0.2/22 dev eth0  # [CONFIG] address + netmask eth0 (A1)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.0.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Lin

**Langkah 1 – [Edit config]** (klik kanan Lin > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Lin: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.0.3
    netmask 255.255.252.0
    gateway 192.237.0.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Lin.

**Langkah 3 – [Web console]** (console Lin; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Lin =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.0.3/22 dev eth0  # [CONFIG] address + netmask eth0 (A1)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.0.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Omnipresence

**Langkah 1 – [Edit config]** (klik kanan Omnipresence > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Omnipresence: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.8.2
    netmask 255.255.252.0
    gateway 192.237.8.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Omnipresence.

**Langkah 3 – [Web console]** (console Omnipresence; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Omnipresence =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.8.2/22 dev eth0  # [CONFIG] address + netmask eth0 (A2)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.8.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Denpa

**Langkah 1 – [Edit config]** (klik kanan Denpa > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Denpa: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.8.3
    netmask 255.255.252.0
    gateway 192.237.8.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Denpa.

**Langkah 3 – [Web console]** (console Denpa; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Denpa =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.8.3/22 dev eth0  # [CONFIG] address + netmask eth0 (A2)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.8.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Masayuki

**Langkah 1 – [Edit config]** (klik kanan Masayuki > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Masayuki: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.4.2
    netmask 255.255.254.0
    gateway 192.237.4.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Masayuki.

**Langkah 3 – [Web console]** (console Masayuki; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Masayuki =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.4.2/23 dev eth0  # [CONFIG] address + netmask eth0 (A3)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.4.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Collective

**Langkah 1 – [Edit config]** (klik kanan Collective > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Collective: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.16.2
    netmask 255.255.254.0
    gateway 192.237.16.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Collective.

**Langkah 3 – [Web console]** (console Collective; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Collective =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.16.2/23 dev eth0  # [CONFIG] address + netmask eth0 (A4)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.16.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Ghost

**Langkah 1 – [Edit config]** (klik kanan Ghost > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Ghost: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.16.3
    netmask 255.255.254.0
    gateway 192.237.16.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Ghost.

**Langkah 3 – [Web console]** (console Ghost; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Ghost =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.16.3/23 dev eth0  # [CONFIG] address + netmask eth0 (A4)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.16.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Alice

**Langkah 1 – [Edit config]** (klik kanan Alice > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Alice: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.24.2
    netmask 255.255.255.0
    gateway 192.237.24.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Alice.

**Langkah 3 – [Web console]** (console Alice; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Alice =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.24.2/24 dev eth0  # [CONFIG] address + netmask eth0 (A5)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.24.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Reika

**Langkah 1 – [Edit config]** (klik kanan Reika > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Reika: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.24.3
    netmask 255.255.255.0
    gateway 192.237.24.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Reika.

**Langkah 3 – [Web console]** (console Reika; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Reika =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.24.3/24 dev eth0  # [CONFIG] address + netmask eth0 (A5)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.24.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Yasuo

**Langkah 1 – [Edit config]** (klik kanan Yasuo > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Yasuo: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.25.2
    netmask 255.255.255.0
    gateway 192.237.25.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Yasuo.

**Langkah 3 – [Web console]** (console Yasuo; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Yasuo =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.25.2/24 dev eth0  # [CONFIG] address + netmask eth0 (A6)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.25.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Miho

**Langkah 1 – [Edit config]** (klik kanan Miho > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Miho: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.25.3
    netmask 255.255.255.0
    gateway 192.237.25.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Miho.

**Langkah 3 – [Web console]** (console Miho; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Miho =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.25.3/24 dev eth0  # [CONFIG] address + netmask eth0 (A6)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.25.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Kuma

**Langkah 1 – [Edit config]** (klik kanan Kuma > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Kuma: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.25.4
    netmask 255.255.255.0
    gateway 192.237.25.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Kuma.

**Langkah 3 – [Web console]** (console Kuma; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Kuma =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.25.4/24 dev eth0  # [CONFIG] address + netmask eth0 (A6)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.25.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host JJ

**Langkah 1 – [Edit config]** (klik kanan JJ > Edit config > tempel > Save):
```
# [EDIT CONFIG] node JJ: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.28.2
    netmask 255.255.255.128
    gateway 192.237.28.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node JJ.

**Langkah 3 – [Web console]** (console JJ; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host JJ =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.2/25 dev eth0  # [CONFIG] address + netmask eth0 (A7)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.28.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host MyuMyu

**Langkah 1 – [Edit config]** (klik kanan MyuMyu > Edit config > tempel > Save):
```
# [EDIT CONFIG] node MyuMyu: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.28.3
    netmask 255.255.255.128
    gateway 192.237.28.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node MyuMyu.

**Langkah 3 – [Web console]** (console MyuMyu; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host MyuMyu =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.28.3/25 dev eth0  # [CONFIG] address + netmask eth0 (A7)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.28.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Lilith

**Langkah 1 – [Edit config]** (klik kanan Lilith > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Lilith: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.22.2
    netmask 255.255.255.128
    gateway 192.237.22.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Lilith.

**Langkah 3 – [Web console]** (console Lilith; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Lilith =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.22.2/25 dev eth0  # [CONFIG] address + netmask eth0 (A8)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.22.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Specter

**Langkah 1 – [Edit config]** (klik kanan Specter > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Specter: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.22.3
    netmask 255.255.255.128
    gateway 192.237.22.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Specter.

**Langkah 3 – [Web console]** (console Specter; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Specter =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.22.3/25 dev eth0  # [CONFIG] address + netmask eth0 (A8)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.22.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Lain

**Langkah 1 – [Edit config]** (klik kanan Lain > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Lain: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.20.66
    netmask 255.255.255.192
    gateway 192.237.20.65
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Lain.

**Langkah 3 – [Web console]** (console Lain; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Lain =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.20.66/26 dev eth0  # [CONFIG] address + netmask eth0 (A9)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.20.65  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Mika

**Langkah 1 – [Edit config]** (klik kanan Mika > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Mika: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.20.67
    netmask 255.255.255.192
    gateway 192.237.20.65
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Mika.

**Langkah 3 – [Web console]** (console Mika; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Mika =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.20.67/26 dev eth0  # [CONFIG] address + netmask eth0 (A9)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.20.65  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Taro

**Langkah 1 – [Edit config]** (klik kanan Taro > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Taro: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.26.2
    netmask 255.255.255.192
    gateway 192.237.26.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Taro.

**Langkah 3 – [Web console]** (console Taro; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Taro =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.26.2/26 dev eth0  # [CONFIG] address + netmask eth0 (A10)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.26.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Chisa

**Langkah 1 – [Edit config]** (klik kanan Chisa > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Chisa: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.21.2
    netmask 255.255.255.192
    gateway 192.237.21.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Chisa.

**Langkah 3 – [Web console]** (console Chisa; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Chisa =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.21.2/26 dev eth0  # [CONFIG] address + netmask eth0 (A11)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.21.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Hodgeson

**Langkah 1 – [Edit config]** (klik kanan Hodgeson > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Hodgeson: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.21.3
    netmask 255.255.255.192
    gateway 192.237.21.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Hodgeson.

**Langkah 3 – [Web console]** (console Hodgeson; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Hodgeson =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.21.3/26 dev eth0  # [CONFIG] address + netmask eth0 (A11)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.21.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Juri

**Langkah 1 – [Edit config]** (klik kanan Juri > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Juri: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.6.2
    netmask 255.255.255.192
    gateway 192.237.6.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Juri.

**Langkah 3 – [Web console]** (console Juri; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Juri =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.6.2/26 dev eth0  # [CONFIG] address + netmask eth0 (A12)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.6.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Semi

**Langkah 1 – [Edit config]** (klik kanan Semi > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Semi: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.20.2
    netmask 255.255.255.224
    gateway 192.237.20.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Semi.

**Langkah 3 – [Web console]** (console Semi; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Semi =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.20.2/27 dev eth0  # [CONFIG] address + netmask eth0 (A13)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.20.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Akashic

**Langkah 1 – [Edit config]** (klik kanan Akashic > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Akashic: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.12.2
    netmask 255.255.255.248
    gateway 192.237.12.1
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Akashic.

**Langkah 3 – [Web console]** (console Akashic; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Akashic =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.12.2/29 dev eth0  # [CONFIG] address + netmask eth0 (A14)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.12.1  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```

## Host Eiri

**Langkah 1 – [Edit config]** (klik kanan Eiri > Edit config > tempel > Save):
```
# [EDIT CONFIG] node Eiri: klik kanan node > Edit config > hapus isi lama > tempel ini > Save

auto eth0
iface eth0 inet static
    address 192.237.20.130
    netmask 255.255.255.248
    gateway 192.237.20.129
    up echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

**Langkah 2 – [GNS3 GUI]** Start (atau Stop lalu Start) node Eiri.

**Langkah 3 – [Web console]** (console Eiri; menaruh file di /root):
```sh
cat > /root/script.sh <<'EOF'
#!/bin/sh
# ===== Host Eiri =====
# Tempel lewat WEB CONSOLE (menaruh file di /root). Jaringan utama sudah dipasang lewat Edit config.
# Uji konektivitas: bash /root/script.sh test   |   Pasang ulang config (cadangan): bash /root/script.sh
# [CONFIG] = sudah ada di config interfaces (di sini hanya cadangan) | [SCRIPT] = hanya ada di script
# Jalankan "bash /root/script.sh" hanya jika IP/route tidak naik dari Edit config.

if [ "$1" = "test" ]; then
  # MODE UJI (tidak mengubah konfigurasi): ping ke SEMUA host dan SEMUA interface router
  ok=0; fail=0
  for t in Karl:192.237.0.2 Lin:192.237.0.3 Omnipresence:192.237.8.2 Denpa:192.237.8.3 Masayuki:192.237.4.2 Collective:192.237.16.2 Ghost:192.237.16.3 Alice:192.237.24.2 Reika:192.237.24.3 Yasuo:192.237.25.2 Miho:192.237.25.3 Kuma:192.237.25.4 JJ:192.237.28.2 MyuMyu:192.237.28.3 Lilith:192.237.22.2 Specter:192.237.22.3 Lain:192.237.20.66 Mika:192.237.20.67 Taro:192.237.26.2 Chisa:192.237.21.2 Hodgeson:192.237.21.3 Juri:192.237.6.2 Semi:192.237.20.2 Akashic:192.237.12.2 Eiri:192.237.20.130 Cyberia.eth1:192.237.28.1 Cyberia.eth2:192.237.28.145 Wired.eth0:192.237.28.146 Wired.eth1:192.237.28.149 Wired.eth2:192.237.28.137 Wired.eth3:192.237.28.129 Navi.eth0:192.237.28.150 Navi.eth1:192.237.24.1 Navi.eth2:192.237.25.1 Navi.eth3:192.237.26.1 Tachibana.eth0:192.237.28.138 Tachibana.eth1:192.237.16.1 Tachibana.eth2:192.237.18.1 Deus.eth0:192.237.18.2 Deus.eth1:192.237.22.1 Deus.eth2:192.237.22.129 Protocol7.eth0:192.237.22.130 Protocol7.eth1:192.237.21.1 Protocol7.eth2:192.237.21.65 Knights.eth0:192.237.21.66 Knights.eth1:192.237.20.129 Knights.eth2:192.237.20.137 Phantoma.eth0:192.237.20.138 Phantoma.eth1:192.237.20.1 Phantoma.eth2:192.237.20.33 Shibuya.eth0:192.237.20.34 Shibuya.eth1:192.237.20.65 Psyche.eth0:192.237.28.130 Psyche.eth1:192.237.0.1 Copland.eth0:192.237.28.131 Copland.eth1:192.237.4.1 Copland.eth2:192.237.6.1 Schumann.eth0:192.237.28.132 Schumann.eth1:192.237.12.1 Schumann.eth2:192.237.12.9 Accela.eth0:192.237.12.10 Accela.eth1:192.237.8.1; do
    n=${t%%:*}; a=${t#*:}
    if ping -c 2 -W 2 "$a" >/dev/null 2>&1; then ok=$((ok+1)); else echo "FAIL  $n ($a)"; fail=$((fail+1)); fi
  done
  if ping -c 1 -W 3 8.8.8.8 >/dev/null 2>&1; then echo "(info) internet 8.8.8.8: OK"; else echo "(info) internet 8.8.8.8: FAIL"; fi
  echo "=== $(hostname): Berhasil $ok, Gagal $fail dari $((ok+fail)) tujuan ==="
  if [ "$fail" -eq 0 ]; then echo "SEMUA OK"; fi
  exit 0
fi

ip addr flush dev eth0  # [SCRIPT] bersihkan IP lama (tidak ada di config)
ip addr add 192.237.20.130/29 dev eth0  # [CONFIG] address + netmask eth0 (A15)
ip link set eth0 up  # [SCRIPT] naikkan interface (di config otomatis oleh ifupdown)
ip route replace default via 192.237.20.129  # [CONFIG] gateway
echo "nameserver 8.8.8.8" > /etc/resolv.conf  # [CONFIG] up echo nameserver
EOF
ls /root
```
