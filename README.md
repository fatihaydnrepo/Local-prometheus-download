## Ubuntu 22.04 üzerine prometheus kurulumu

Sistem paketlerini güncelliyoruz
 > sudo apt update

Prometheus sistemi için kullanıcı grubu oluşturuyoruz.
> sudo groupadd --system prometheus

Oluşturduğumuz gruba user ekliyoruz
>sudo useradd -s /sbin/nologin --system -g prometheus prometheus

Directory oluşturuyoruz (ana dizin)
>sudo mkdir /etc/prometheus

Aynı şekilde alt dizinler içinde directory oluşturuyoruz.
>sudo mkdir /var/lib/prometheus

Aşağıdaki adresde bulunan tar dosyasını indiriyoruz.
![enter image description here](https://raw.githubusercontent.com/fatihaydnrepo/Local-prometheus-grafana/main/Screenshot%20from%202023-05-05%2000-27-08.png)

Tar dosyasını çıkartıyoruz.
>tar xvf prometheus*.tar.gz

Aşağıdaki kod ile dizin içerisine giriyoruz.
>cd prometheus*/

Dosyaları /usr/local/bin konumuna taşımak için aşağıdaki komutu kullanıyoruz.
>sudo mv prometheus promtool /usr/local/bin/

Prometheus'un çalıştığını doğruluyoruz
>prometheus --version

  
Kurulum başarıyla tamamlandığında konfigürasyon şablonunu Prometheus konfigürasyonu için /etc dizinine taşıyoruz
>sudo mv prometheus.yml /etc/prometheus/prometheus.yml
>sudo mv consoles/ console_libraries/ /etc/prometheus/ 
> cd $HOME

Prometheus.yml dosyasını ihtiyacımıza uygun şekilde tanımlıyoruz. Ben şimdilik ubuntu sistem metriklerini localhost:9100 portundan ulaşmak için tanımlama yapacağım.
> sudo nano /etc/prometheus/prometheus.yml

```json 
global:
  scrape_interval: 15s
  scrape_timeout: 10s

scrape_configs:
  - job_name: 'ubuntu'
    static_configs:
      - targets: ['localhost:9100']
```

Prometheus'u otomatik olarak başlatmak için prometheus systemd hizmet dosyasını oluşturuyoruz.
> sudo nano /etc/systemd/system/prometheus.service

```json 
	[Unit] 
	Description=Prometheus
	Documentation=https://prometheus.io/docs/introduction/overview/ 
	Wants=network-online.target 
	After=network-online.target [Service] 
	User=prometheus 
	Group=prometheus 
	Type=simple 
	ExecStart=/usr/local/bin/prometheus \ --config.file /etc/prometheus/prometheus.yml \
	--storage.tsdb.path /var/lib/prometheus/ \ 
	--web.console.templates=/etc/prometheus/consoles \ 
	--web.console.libraries=/etc/prometheus/console_libraries

    [Install] 
    WantedBy=multi-user.target
   ```
Sistem yeniden başlatıldıktan sonra prometheus'u otomatik olarak başlatmak için prometheus'u etkinleştiriyoruz
> sudo systemctl daemon-reload
> sudo systemctl enable --now prometheus

9090 portu için izin veriyoruz.
>sudo ufw allow 9090/tcp


http://localhost:9090/ adresinden prometheus'a erişim sağlıyoruz.
![enter image description here](https://raw.githubusercontent.com/fatihaydnrepo/Local-prometheus-download/main/Screenshot%20from%202023-05-05%2001-00-12.png)

