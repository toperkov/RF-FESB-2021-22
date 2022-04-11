# Lab 5 - Analiza diska

### Ubacivanje custom Kali VM slike

- Instalirajte [Virtualbox](https://www.virtualbox.org/) i VM VirtualBox Extension Pack
- Pokrenite Custom Kali VM = Kali (2020.4) kojeg će profesor pripremiti za vas

Pri pokretanju Kali VM-a primjetit ćete da se na računalu nalazi image diska ``Disk_Image_ID-202103.27.001``

### HASH
Izračunajte MD5 i SHA1 hard diska
```
md5sum <disk image>
sha1sum <disk image>
```

### fdisk
fdisk je jedan od alata s kojim očitate partition tablicu.
``fdisk –l <disk image>``

### fsstat
fsstat daje file system information navedene particije.
``fsstat –o <offset number> <disk image>``

### losetup
Za mountanje slike diska koristimo losetup naredbu. Pri njenom pozivu se pokreće kao uređaj na računalu. 
``sudo losetup --partscan --find --show --read-only <Disk_Image_ID-202103.27.001 path>``

### Registry
Kopirajte Registry files iz mountanog diska u vlastiti ‘Registry Files’ direktorij.
```
cp /media/xx/yy/Windows/System32/config/SAM Registry_Files
cp /media/xx/yy/Windows/System32/config/SECURITY Registry_Files
cp /media/xx/yy/Windows/System32/config/SOFTWARE Registry_Files
cp /media/xx/yy/Windows/System32/config/SYSTEM Registry_Files
cp /media/xx/yy/Windows/Users/Kamryn/NTUSER.DAT Registry_Files
```
gdje je xx korisničko ime, dok je yy ID mountane slike.

### Regripper
Regripper parsira registry files upotrebom pluginova. Pomoću njih se može saznati informacija koju je Regripper skupio. Pomoću naredbe ``rip.pl –l`` dobijete listu svih pluginova (preporuka napraviti output u textualnu datoteku ili koristiti ‘| more’ budući da je output prevelik).

Korištenje: ``rip.pl –r <registry hive> -p <plugin>``
Koja je system timezone: ``rip.pl -r SYSTEM -p timezone``
Koji je operativni sustav instaliran na disku ``rip.pl -r SOFTWARE -p winver``
Ime sustava: ``rip.pl -r SYSTEM -p compname``
Koji su accounti na računalu: ``rip.pl -r SAM -p samparse | grep -E 'Username|Created|Date' --color=none``
Koji se korisnik zadnji logirao i kada ``rip.pl -r SOFTWARE -p lastloggedon``
Kada se računalo zadnji puta ugasilo: ``rip.pl -r SYSTEM -p shutdown``
Što je korisnik zadnje otvorio ``rip.pl -r NTUSER.DAT -p recentdocs``

### fls
Lista file-ova i direktorija na danoj particiji: ``fls -o <offset number> <disk image> -r -l -p -z <timezone> >> file_dir.csv``

### Grep
Traženje torrent aplikacija ``grep -F '.exe' file_dir.csv | grep -i 'torrent' | cut -f 1,2,4,5``
Traženje mp3 datoteka ``grep -F '.mp3' file_dir.csv | grep -F 'torrent' | cut -f 1,2,4,5``

### Dodatak
```
echo -e 'File Type and Metadata\tName\tLast Modified\tLast Accessed\tLast Changed\tCreated' >> file_dir_mod.csv
cat file_dir.csv >> file_dir_mod.csv
```

