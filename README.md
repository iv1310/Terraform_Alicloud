# Terraform_Alicloud
  ***Terraform*** adalah tool open-source yang bisa digunakan untuk melakukan building, changing, dan versioning secara automasi pada perangkat infrastrktur dengan cara aman dan se-efisien mungkin. Untuk menentukan componen yang perlu diprovision tinggal menambahkannya saja dalam terraform file. Fiitur utama dari terraform adalah infrastructure as code, execution plans, resource graph, dan change automation

## Pre-requisites
1. Dijalankan di Ubuntu 19.04
2. Terraform versi 0.11.7
3. Memiliki accessKey dan secretKey alicloud

## Instalasi
1. Download terraform versi 0.11.7.  
`cd /tmp && wget https://releases.hashicorp.com/terraform/0.11.7/terraform_0.11.7_linux_amd64.zip`

2. Kemudian extract file compress hasil download.  
`unzip terraform_0.11.7_linux_amd64.zip`

3. Kemudian set permission executable pada file terraform.  
`chmod +x ./terraform`

4. Kemudian coba jalankan file executablenya untuk melihat versi dari terraform.  
`./terraform version`  
**Output**:

5. Kemudian pindahkan atau copy file executable kedalam directory bin.  
`mv terraform /usr/local/bin`  
Dan terraform sudah siap untuk digunakan.
