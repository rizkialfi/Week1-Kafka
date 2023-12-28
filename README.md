# Week 1-Kafka
Learning Kafka for the first week


Hallo Disini saya Belajar cara bagaimana Menginstall Kafka Standalone dengan Zookeeper didalamnya
Dan saya ingin menggunakan systemd untuk jalan di background

Kita akan mencoba install zookeeper untuk 3 Broker/Node pada 3 VM Kita jadi kalian harus menginstall <strong>Apache Kafka</strong>

Untuk Menjalankan Manual tetapi tidak jalan dibackground bisa menggunakan  :
![image](https://github.com/rizkialfi/Week1-Kafka/assets/25836490/5418a6cb-727b-4bcf-a81f-372a18de62bf)


Tetapi saya ingin menjalankan menggunakan systemd, so here is the step :

Pertama : 

1. Installasi Kafka dengan link berikut :
[Download](https://dlcdn.apache.org/kafka/3.6.1/kafka_2.13-3.6.1.tgz)
2. Kalian Bisa Menggunakan Wget untuk install nya
Contoh :
```bash
wget https://dlcdn.apache.org/kafka/3.6.1/kafka_2.13-3.6.1.tgz
```
3. Lakukan Unzip menggunakan command tar
```bash
tar -xzvf kafka_2.13-3.6.1.tgz
```

4. Lakukan rename file dengan cara
```bash
mv kafka_2.13-3.6.1.tgz kafka_baru
```
6. Jika sudah kemudian kita bisa edit Config pada Kafka yang ada pada folder tersebut, pertama kita akan edit config ```server.properties```
7. Buka File nya dengan cara
```bash
vi /home/kafka/kafka_baru/config/server.properties
```
> [!NOTE]  
> Ubah nama file server.properties kalian pada setiap VM
> Sebagai Contoh :

> VM 1 : server.properties
> VM 2 : server-2.properties
> VM 3 : server-3.properties

<bold>Cara mengubahnya dengan cara dibawah ini</bold>

```bash
cp server.properties server-2.properties
```
Kemudian Kalian bisa melanjutkan setiap dibawah ini, edit tiap file yang tadi namanya sudah di ubah oleh kalian

8. Nantinya Kalian akan menemukan isi file seperti ini :
![image](https://github.com/rizkialfi/Week1-Kafka/assets/25836490/3de31ffb-34aa-43ec-8736-4cda5db1dcde)

9. Dalam gambar diatas, pertama tanda panah bisa kalian biarkan broker ID nya 0 dikarenakan ini VM 1, dan kalian harus mengubah IP Listener dan Advertised listener kalian menggunakan IP VM kalian.
10. Kemudian ganti directory Log pada setiap VM kalian

![image](https://github.com/rizkialfi/Week1-Kafka/assets/25836490/014d4d8d-8bc8-4afa-8029-5a94928f0cb6)

```bash
Example :
Kalian Menggunakan 3 VM
Pada VM Pertama ubah menjadi : /var/log/kafka-logs1
Pada VM Kedua Ubah menjadi : /var/log/kafka-logs2
Pada VM Ketiga ubah menjadi : /var/log/kafka-logs3
```

11. Dan jangan Lupa ubah dan tambahkan IP VM anda di bagian ```zookeeper.connect``` seperti digambar dibawah ini
![image](https://github.com/rizkialfi/Week1-Kafka/assets/25836490/afb8d14c-2bde-41bb-b567-49c5afa84a77)

> [!NOTE]  
> Ubah setiap bagian ```zookeeper.connect``` disetiap VM kalian pada bagian service.properties

12. Sekarang Kalian Pergi untuk membuat directory ```/var/log/kafka-log1``` dengan cara
```bash
mkdir -p /var/log/kafka-log1 ##untuk membuat folderna
sudo chown -R kafka:kafka /var/log/kafka-log1 #mengubah akses pada folder/direktory ke user kafka
sudo chmod -R 755 /var/log/kafka-log1 #mengubah permission pada folder tersebut

> Lakukan Step diatas pada setiap VM kalian, dan sesauikan namanya seperti step 10 diatas
```
13. Sekarang kita bisa mengubah akses direktory zookeeper kalian pada ```zookeeper.properties``` yang terletak di direktory ```/config/zookeeper.properties```. dan berikut merupakan isi file ```zookeeper.properties```
![image](https://github.com/rizkialfi/Week1-Kafka/assets/25836490/ab1678ff-a067-42a0-833c-7beb5b49c72b)

    
15. Dalam Kotak dan tanda panah diatas kalian bisa rubah setiap VM kalian dengan nama yang berbeda seperti diatas, kemudian kalian save filenya
16. Step selanjutnya kalian harus membuat directory yang tadi sudah kalian ubah pada file zookeeper.properties
```bash
mkdir -p /data/zookeeper ##untuk membuat foldernya
sudo chown -R kafka:kafka /data/zookeeper #mengubah akses pada folder/direktory ke user kafka
sudo chmod -R 755 /data/zookeeper #mengubah permission pada folder tersebut

> Kalian ubah tiap VM kalian dan samakan seperti diatas
```
17. Jika kalian sudah rubah settingan dan confignya kalian bisa membuat dan menjalankan service zookeeper.service dan kafka.service nya menggunakan ```systemd```. Berikut ini caranya :
```bash
> Buat File zookeeper.servicenya terlebih dahulu dengan cara :
sudo /vi/etc/systemd/system/kafka-zookeeper.service
```
18. Kemudian kalian bisa isi file tersebut dengan script berikut ini :
```bash
[Unit]
Description=Apache Kafka - ZooKeeper
After=network.target

[Service]
Type=simple
User=kafka
Group=kafka
ExecStart=/home/kafka/kafka_baru/bin/zookeeper-server-start.sh /home/kafka/kafka_baru/config/zookeeper.properties
ExecStop=/home/kafka/kafka_baru/bin/zookeeper-server-stop.sh
Restart=on-abort

[Install]
WantedBy=multi-user.target

```
19. Jika sudah sekarang kalian bisa membuat file ```kafka.service```nya dengan cara :
```bash
> sudo /vi/etc/systemd/system/kafka.service
```
20.  Lalu kalian isi dengan script dibawah ini :
```bash
[Unit]
Description= Apache Kafka - Zookeeper Service
After=network.target

[Service]
Type=simple
User=kafka
ExecStart=/home/kafka/kafka_baru/bin/kafka-server-start.sh /home/kafka/kafka_baru/config/server.properties
ExecStop=/home/kafka/kafka_baru/bin/kafka-server-stop.sh
Restart=on-abnormal

[Install]

```


> Note
> Kalian bisa ubah pada bagian ```/home/kafka/kafka_baru/config/server.properties``` ganti dengan nama file ```server.properties```nya pada tiap VM yang tadi sudah anda ubah

21. Jika sudah kalian bisa mereload daemon, enable dan menjalankan service kafka dan zookeepernya dengan cara :

```bash
>systemctl daemon-reload
>systemctl enable kafka-zookeeper
>systemctl start kafka-zookeeper
>systemctl enable kafka
>systemctl start kafka

```
22. Jalankan command diatas pada setiap VM, kemudian kalian bisa check statusnya dengan cara dibawah ini :

>Zookeeper
![image](https://github.com/rizkialfi/Week1-Kafka/assets/25836490/5d9bbf0c-54eb-4da8-801f-2bc3cfe39f81)

>Kafka
![image](https://github.com/rizkialfi/Week1-Kafka/assets/25836490/8024c5be-ee4e-49c6-be40-210cae9f199e)




24. Jika bertuliskan Active dan berwarna hijau tandanya sudah selesai dan sudah berjalan
25. Pastikan anda menjalankan pada TIAP VM Kalian yang sudah kalian buat tadi sesuai cara diatas




Tutorial Selanjutnya Cara Membuat Topic dan Membuat Event : 

![image](https://github.com/rizkialfi/Week1-Kafka/assets/25836490/830b0f30-246c-4a71-8f3b-df082c4bc9b0)

1. Sebagai gambaran diatas saya coba membuat topic ```topik-percobaan```
Here is the command  :
```bash
> /home/kafka/kafka_baru/bin/kafka-topics.sh --create --topic topik-percobaan --bootstrap-server localhost:9092 #topik-percobaan adalah nama topik yang akan kita buat, kita bisa mengubahnya juga

```


2. Setelah itu kita bisa check untuk topic yang telah buat dengan cara describe the topic dengan Command :
```bash
> /home/kafka/kafka_baru/bin/kafka-topics.sh --describe --topic topik-percobaan --bootstrap-server 172.18.46.46:9092
```

Berikut Hasil nya : 
![image](https://github.com/rizkialfi/Week1-Kafka/assets/25836490/8da7b438-7cfd-4783-80b0-21860ec0eb0b)

4. Jika sudah mengecek topic yang sudah dibuat, kita bisa menambahkan sebuah event kedalam topic, dengan cara :
```bash
> /home/kafka/kafka_baru/bin/kafka-console-producer.sh --topic new-topic --bootstrap-server 172.18.46.46:9092

#Pada Command Diatas kita menggunakan script bash produce untuk membuat topicnya
#Untuk Cek Event pada step selanjutnya kita akan menggunakan script consumer
```
![image](https://github.com/rizkialfi/Week1-Kafka/assets/25836490/3463b90c-70a2-4f06-ab55-0d5e3c6c1947)

4. Sekarang saatnya check event yang sudah kita tambahkan kedalam topic dengan menggunakan script consumer dengan cara :
```bash
> /home/kafka/kafka_baru/bin/kafka-console-consumer.sh --topic new-topic --from-beginning --bootstrap-server 172.18.46.46:9092
```
6. Berikut kemudian hasil nya :
![image](https://github.com/rizkialfi/Week1-Kafka/assets/25836490/95a31956-d787-4ffe-b9af-1989acd010e7)


Terakhir kalian bisa melakukan ```CTRL + C``` setelah menambahkan atau cek event yang sudah ditambahkan :)



