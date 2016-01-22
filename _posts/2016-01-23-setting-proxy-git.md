---
title : 'Pengaturan proxy untuk Git'
tags  : 'git vcs'
---
Lagi iseng di kantor, mau push ke [Github](http://github.com) eh nggak bisa. 
Ternyata si _git_ ini tidak membaca pengaturan _proxy_ yang diatur lewat 
_shell variable_ atau dari pengaturan _proxy_ gnome. Jadi bagaimana mengatur
git agar menggunakan _proxy_ ketika mengakses _server_? begini caranya :grin: :
<!--more-->
Ketik perintah ini pada terminal/_command line_ dengan mengubah terlebih dahulu
proxy.mycompany:port menjadi alamat atau _ip addrress_ dari proxy server dan _port_ 
nya 
<pre class="cli-command">
<span class="cli-env">~$</span> export HTTP_PROXY=http://proxy.mycompany:port
<span class="cli-env">~$</span> git config --global http.proxy $HTTP_PROXY
</pre>
Catatan: Semua perintah export tidak permanen, sehingga setiap kali komputer 
dinyalakan kembali command export harus dijalankan lagi. Untuk menjadikan perintah
tersebut permanen bisa menambahkan 
<pre class="cli-command">
export HTTP_PROXY=http://proxy.mycompany:port
</pre>
pada file `~/.bashrc` untuk bash atau `~/.zshrc` untuk zsh.
Apabila proxy yang digunakan menggunakan _username_ dan _password_ untuk 
mengotentikasi pengguna, maka perintahnya menjadi:
<pre class="cli-command">
<span class="cli-env">~$</span> export HTTP_PROXY=http://username:password@proxy.mycompany:port
<span class="cli-env">~$</span> git config --global http.proxy $HTTP_PROXY
</pre>

Perintah di atas hanya dapat digunakan untuk menarik data (_pull_) dari _server_
git. Apabila kita ingin mengunggah data (_push_) ke _server_ git maka kita juga
harus mengatur proxy pada ssh, karena koneksi ke _server_ umumnya menggunakan ssh.
Untuk mengatur proxy pada ssh dapat melakukan tahapan berikut:

####1. Install corkscrew
<pre class="cli-command">
<span class="cli-env">~$</span> sudo pacman -S corkscrew
</pre>
Perintah ini hanya berlaku untuk Arch Linux, untuk distro lain atau windows 
silahkan mencari bantuan dengan mesin pencari Google cara menginstall corkscrew

####2. Atur proxy ssh
Edit dokumen `~/.ssh/config` apabila dokumen belum tersedia dapat membuat dokumen 
baru dan tambahkan.
<pre class="cli-command">
ProxyCommand /bin/corkscrew 10.203.1.11 8080 %h %p
</pre>

####3. Atur koneksi Git dengan ssh+proxy
Umumnya koneksi yang melewati proxy juga mengalami pemblokiran untuk akses melewati
beberapa _port_. Seperti kasus di kantor saya yang port ssh-nya diblokir, maka 
dapat melakukan koneksi ssh melalui _port_ lain. Github menyediakan koneksi ssh 
melewati port HTTPS, atau 443. Untuk menggunakannya dapat mengedit `~/.ssh/config` 
dan tambahkan: 
<pre class="cli-command">
Host github.com
  Hostname ssh.github.com
  Port 443
</pre>

Untuk menguji apakah koneksi berhasil atau tidak, dapat menjalankan perintah 
berikut:
<pre class="cli-command">
<span class="cli-env">~$</span> ssh -T git@github.com
Hi username! You've successfully authenticated, but GitHub does not
provide shell access.
</pre>

###Menghapus pengaturan proxy
Untuk menghapus pengaturan proxy yang sudah dibuat sebelumnya, dapat menjalankan 
perintah:
<pre class="cli-command">
<span class="cli-env">~$</span> export HTTP_PROXY=""
<span class="cli-env">~$</span> git config --global --unset core.gitproxy
</pre>
Berikutnya hapus dokumen `~/.ssh/config`. Apabila berencana menggunakan proxy lagi
sebaiknya dokumen ini di-_backup_ terlebih dahulu dan dikembalikan lagi apabila
dibutuhkan.