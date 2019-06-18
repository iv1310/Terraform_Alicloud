# Terraform_Alicloud
  ***Terraform*** adalah tool open-source yang bisa digunakan untuk melakukan building, changing, dan versioning secara automasi pada perangkat infrastrktur dengan cara aman dan se-efisien mungkin. Untuk menentukan componen yang perlu diprovision tinggal menambahkannya saja dalam terraform file. Fiitur utama dari terraform adalah infrastructure as code, execution plans, resource graph, dan change automation

## Pre-requisites
1. Dijalankan di Ubuntu 19.04
2. Terraform versi 0.11.7
3. Memiliki accessKey dan secretKey alicloud

## Instalasi
1. Download terraform versi 0.11.7.  
`$ cd /tmp && wget https://releases.hashicorp.com/terraform/0.11.7/terraform_0.11.7_linux_amd64.zip`

2. Kemudian extract file compress hasil download.  
`$ unzip terraform_0.11.7_linux_amd64.zip`

3. Kemudian set permission executable pada file terraform.  
`$ chmod +x ./terraform`

4. Kemudian coba jalankan file executablenya untuk melihat versi dari terraform.  
`$ ./terraform version`  
**Output**:   
![Terraform version!](/images/ter_29.png "Terraform version")
5. Kemudian pindahkan atau copy file executable kedalam directory bin.  
`$ mv terraform /usr/local/bin`  
Dan terraform sudah siap untuk digunakan.

## Usage
#### - Basic 
1. Pertama sekali tambahkan ***accessKey, secretKey, dan region*** pada environment variabel, untuk memudahkan penggunaan alicloud sebagai provider. Tambahkan pada file: *~/.bashrc, ~/.zshrc,dll*. Pada contoh berikut menggunakan file *~/.zshrc*:   
![Set Environment Variable!](/images/ter_15.png "Export environment variables")

2. Kemudian buat file dan directory yang akan digunakan.  
`$ mkdir example; cd example; touch main.tf`

3. Kemudian pada percobaan 1, percobaan akan melakukan provisioning instance dengan menggunakan security_groups yang telah tersedia. Berikut contoh file konfigurasi terraform yang akan digunakan:   
```
provider "alicloud"{   
}

resource "alicloud_instance" "example-ecs"{
 instance_type="ecs.g5.large"
 image_id="ubuntu_16_04_64_20G_alibase_20190513.vhd"
 security_groups=["sg-k1agjazrdjzag37rx3ab"]
 vswitch_id="vsw-k1a38rwbjbm1hk7z53cem"
}
```

4. Kemudian lakukan inisiasi, yang bertujuan untuk mendowload plugin dari provider(*alicloud*) yang digunakan dengan perintah, `$ terraform init`  
![Terraform init!](/images/ter_18.png "Initiation plugin provider")

5. Kemudian untuk memastikan resource apa saja yang akan diproses nantinya oleh terraform, gunakan perintah `$ terraform plan`   
![Terraform plan!](/images/ter_19.png "Check resources")

6. Kemudian setelah memastikan setiap resource yang akan diproses sudah benar, tinggal jalankan terraform untuk memprovision setiap resource yang terdaftar, gunakan perintah `$ terraform apply`. Dan ketika muncul prompt untuk konfirmasi, isi dengan yes.  
![Terraform apply!](/images/ter_21.png "Provisioning process")

7. Kemudian cek pada aliyun console untuk memastikan apakah instance sudah terprovision atau belum.   
![Aliyun Console!](/images/ter_22.png "Check aliyun console")

8. Kemudian, terraform juga menyediakan fitur state information yang berisi track record konfigurasi yang telah dijalankan oleh terraform. State disimpan dalam JSON file pada local machine dengan nama *terraform.tfstate*.    
![Terraform state!](/images/ter_23.png "Terraform state information")

9. Atau untuk melihat state bisa juga dengan perintah `$ terraform show`    
![Terraform show!](/images/ter_24.png "Terraform state information")

10. Selanjutnya, ketika ingin menghapus semua resource yang sebelumnya telah diprovision, gunakan perintah `$ terraform destroy`    
![Terraform destroy!](/images/ter_25.png "Destroy resources")

#### - Medium


## Referensi
1. [Terraform documentation](https://www.terraform.io/docs/index.html)
2. [Download terraform](https://releases.hashicorp.com/terraform/)
3. [Alicloud](https://www.alibabacloud.com/help)
