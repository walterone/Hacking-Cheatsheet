## File TARs
```bash
#Estrazione archivi:
tar -xvzf file.tgz / test.tar.gz
tar -xvzj file.bz2 / test.tar.bz2
tar -xvf file.tar -C /home/public_html/videos/

#Creazione Archivi:
tar -cvf tfile.tar /home/tecmint/
tar -cvzf file.tar.gz /home/MyImages

#Visualizza file all'interno:
tar -tvf <archivio.ext>

```

## File .gz (Gunzip)
```bash
#Decompress
gunzip -d file.gz
gzip -d test.gz

#Read & Save
zcat rockyou.txt.gz > rockyou.txt
```

