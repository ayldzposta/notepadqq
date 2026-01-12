build/out/release klasöründe yaptığınız manuel düzeltmeler (html dosyalarını kopyalamak vb.) olduğu için, standart sudo make install komutunu kullanmak yerine, elimizdeki çalışan bu klasörü sistem klasörüne taşıyarak kurulum yapmak en güvenli yoldur. Böylece yaptığınız yamalar kaybolmaz.
Bunu 3 adımda temiz bir şekilde yapalım:
1. Adım: Çalışan Sürümü /opt Klasörüne Taşıma
Linux'ta elle kurulan programlar genellikle /opt klasöründe tutulur.
Önce terminalde proje ana dizininde olduğunuzdan emin olun ve şu komutu girin:

Bash


# Eğer daha önce deneme yaptıysanız eskiyi siler
sudo rm -rf /opt/notepadqq

# Çalışan release klasörünü /opt altına taşır ve adını 'notepadqq' yapar
sudo cp -r build/out/release /opt/notepadqq

# Dosya izinlerini ayarlar (Herkes okuyabilsin ve çalıştırabilsin)
sudo chmod -R 755 /opt/notepadqq


2. Adım: Terminalden Çalıştırmak İçin Kısayol (Symlink) Oluşturma
Terminal'e sadece notepadqq yazdığınızda açılması için, /opt içindeki çalıştırılabilir dosyayı sistemin /bin klasörüne bağlayacağız.

Bash


# Mevcut bir link varsa siler
sudo rm -f /usr/bin/notepadqq

# Yeni linki oluşturur
sudo ln -s /opt/notepadqq/lib/notepadqq-bin /usr/bin/notepadqq


Not: Normalde bin/notepadqq adında bir başlatıcı script olur ancak sizin son komutunuzda lib/notepadqq-bin dosyasını LD_LIBRARY_PATH ile çalıştırdığınızı gördüm. Eğer doğrudan çalışınca kütüphane hatası verirse, özel bir başlatıcı script yazmamız gerekebilir. Şimdilik bunu deneyin.
3. Adım: Başlat Menüsüne Ekleme (.desktop Dosyası)
Uygulamanın menüde ikonlu görünmesi için bir masaüstü dosyası oluşturmalıyız.
Aşağıdaki komutu kopyalayıp terminale yapıştırın:

Bash


sudo bash -c 'cat > /usr/share/applications/notepadqq.desktop <<EOF
[Desktop Entry]
Name=Notepadqq
Comment=Notepad++ like editor
Exec=/opt/notepadqq/lib/notepadqq-bin %F
Icon=/opt/notepadqq/share/icons/hicolor/48x48/apps/notepadqq.png
Type=Application
Terminal=false
Categories=Development;TextEditor;
StartupNotify=true
MimeType=text/plain;
EOF'


(Not: İkon yolu varsayılan olarak share/icons... yolundadır. Eğer ikon görünmezse bu yolu daha sonra düzeltebiliriz.)
Test Zamanı
Terminali kapatıp açın.
Komut satırına notepadqq yazın. Açılıyor mu?
Uygulama menüsünü (Başlat menüsü) açıp "Notepadqq" diye aratın. İkonu geldi mi?
Eğer açılırken yine "Library not found" gibi bir hata verirse haber verin, /opt/notepadqq içine küçük bir başlangıç scripti yazalım.
