# Pemeriksaan dokumen dan bukti

## Perhitungan

Seluruh 25 subnet pada spreadsheet telah diperiksa: network ID valid, broadcast dan rentang usable sesuai, kapasitas mencukupi, serta tidak ada overlap. Prefix tiap subnet adalah ukuran minimum untuk kebutuhan IP yang tertera. Total kebutuhan host dan interface router adalah 2.814. Jumlah seluruh blok adalah 4.188 alamat; termasuk 50 alamat network/broadcast dan 1.324 alamat usable yang tidak dipakai oleh kebutuhan simulasi. Sisa kapasitas ini merupakan akibat ukuran blok biner VLSM.

## Hal yang harus dicocokkan sebelum pengumpulan

1. Screenshot Karl menampilkan subnet mask 255.255.255.0. A1 menurut perhitungan dan konfigurasi Psyche adalah /22, sehingga mask host Karl yang sesuai rencana ialah **255.255.252.0**. IP 192.237.0.2 dan gateway 192.237.0.1 tetap. Ping Lin ke Karl saja tidak membuktikan mask benar, karena keduanya berada pada rentang yang saling tercakup. Belum ada screenshot perbaikan Karl.
2. Sel C27 pada sheet Rute masih berisi 1395. Total C2:C26 adalah **2814**; pada file kerja dapat diperbaiki dengan `=SUM(C2:C26)`. Spreadsheet asli dalam paket sengaja dipertahankan tanpa perubahan.
3. Screenshot link yang down menunjukkan tahap awal. Status up/up harus dinilai dari konfigurasi akhir dan hasil pengujian, bukan disimpulkan dari screenshot tersebut.
4. File .pkt disalin dari lampiran tanpa modifikasi. Penyusun laporan belum membuka ulang file tersebut pada Packet Tracer. Bukti CLI dan screenshot tidak dianggap sebagai audit seluruh isi file biner.
5. Bukti ping mencakup pasangan yang tertulis. Klaim semua pasangan node saling terhubung memerlukan pengujian tambahan.

## Batas cakupan

Laporan ini membahas CPT dengan VLSM. GNS3 dan perhitungan CIDR menjadi bagian anggota kelompok lain. Konfigurasi NAT/Cloud belum ditunjukkan dalam bukti ini. Tidak ada klaim koneksi internet berhasil. Routing yang tersedia didokumentasikan, tetapi optimalitas seluruh tabel routing belum dapat dibuktikan tanpa running-config lengkap semua router.
