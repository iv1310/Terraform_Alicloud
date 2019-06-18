# Terraform_Alicloud
[***Terraform***](https://www.terraform.io/intro/index.html) adalah tool open-source yang bisa digunakan untuk melakukan building, changing, dan versioning secara automasi pada perangkat infrastrktur dengan cara aman dan se-efisien mungkin. Untuk menentukan componen yang perlu diprovision tinggal menambahkannya saja dalam terraform file. Fiitur utama dari terraform adalah infrastructure as code, execution plans, resource graph, dan change automation

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

3. Kemudian pada percobaan 1, percobaan akan melakukan provisioning instance dengan menggunakan security_groups yang telah tersedia. Berikut contoh file konfigurasi [`main.tf`](main_1.tf.bak) terraform yang akan digunakan:   
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
1. Untuk percobaannya selanjutnya akan membahas penggunaan variable, pembuatan resource yang lebih banyak seperti [security group](https://www.alibabacloud.com/help/doc-detail/25387.htm?spm=a2c63.l28256.a3.99.722b7453Tm89wM), rules, [vpc](https://www.alibabacloud.com/help/doc-detail/34217.htm?spm=a2c63.l28256.a3.2.3b48639doJscRG), vswitch, dan attach key pair. Kemudian buat directory baru dan file file yang diperlukan, berikut konfigurasi dari terraform filenya [`main.tf`](main_2.tf.bak):   
```
provider "alicloud"{
}

resource "alicloud_vpc" "main"{
	cidr_block="${var.vpc_cidr}"
}

resource "alicloud_vswitch" "main"{
	vpc_id="${alicloud_vpc.main.id}"
	cidr_block="${var.vswitch_cidr}"
	availability_zone="${var.zone}"
	depends_on=["alicloud_vpc.main"]
}

resource "alicloud_route_entry" "entry"{
	route_table_id="${alicloud_vpc.main.router_table_id}"
	destination_cidrblock="0.0.0.0/0"
	nexthop_type="Instance"
	nexthop_id="${alicloud_instance.instance-1.id}"
}

resource "alicloud_instance" "instance-1"{
	availability_zone = "${var.zone}"
	instance_type = "${var.instance_nat_type}"
	image_id = "${var.image}"
	instance_name = "Test-terraform"
	security_groups = ["${alicloud_security_group.group.id}"]
	vswitch_id = "${alicloud_vswitch.main.id}"
  	internet_charge_type  = "PayByTraffic"	
	internet_max_bandwidth_out=2
	instance_charge_type="PostPaid"
}

resource "alicloud_key_pair_attachment" "attachment"{
	key_name="ivan-intern"
	instance_ids=["${alicloud_instance.instance-1.*.id}"]
}

resource "alicloud_security_group" "group"{
	name="terraform_test_group"
	description="New Security Group"
	vpc_id="${alicloud_vpc.main.id}"
}

resource "alicloud_security_group_rule" "allow_in"{
	security_group_id="${alicloud_security_group.group.id}"
	type="ingress"
	cidr_ip="0.0.0.0/0"
	policy="accept"
	ip_protocol="all"
	nic_type="intranet"
	port_range="-1/-1"
	priority=1
}

resource "alicloud_security_group_rule" "allow_out"{
	security_group_id="${alicloud_security_group.group.id}"
	type="egress"
	cidr_ip="0.0.0.0/0"
	policy="accept"
	ip_protocol="all"
	nic_type="intranet"
	port_range="-1/-1"
	priority=1
}

resource "alicloud_security_group_rule" "ssh"{
	security_group_id="${alicloud_security_group.group.id}"
	type="ingress"
	port_range="22/22"
	ip_protocol="tcp"
	cidr_ip="0.0.0.0/0"
	policy="accept"
}

```

2. Kemudian, pada percobaan ini akan menerapkan penggunaan [variable](https://learn.hashicorp.com/terraform/getting-started/variables) untuk menampung nilai-nilai yang konstan, berikut contohnya [`variables.tf`](variables_2.tf.bak):    
```
variable "vpc_cidr"{
	default="10.1.0.0/21"
}

variable "vswitch_cidr"{
	default="10.1.1.0/24"
}

variable "zone"{
	default="ap-southeast-5a"
}

variable "image"{
	default="ubuntu_16_04_64_20G_alibase_20190513.vhd"
}

variable "instance_nat_type"{
	default="ecs.g5.large"
}

```

3. Kemudian jalankan `$ terraform init`.    
![ALT HERE!](/images/ter_32.png "Initiation plugin provider")

4. Selanjutnya jalankan perintah `$ terraform plan` untuk memastikan resource yang akan di provision oleh terraform.    
![ALT HERE!](/images/ter_33.png "Terraform plan")

5. Kemudian setelah memastikan setiap resource yang akan diprovision sudah benar, lanjutkan dengan perintah `$ terraform apply`.    
![ALT HERE!](/images/ter_34.png "Terraform plan")

6. Kemudian coba cek resource pada aliyun console.   
 - Instance 
 ![ALT HERE!](/images/ter_35.png "Instance Resource")   
 - Security_groups
 ![ALT HERE!](/images/ter_36.png "Security Groups Resource")    
 - VPC
 ![ALT HERE!](/images/ter_37.png "VPC Resource")   
 - VSwitch
 ![ALT HERE!](/images/ter_38.png "VSwitch Resource")   
 - Route Table
 ![ALT HERE!](/images/ter_39.png "Route Table Resource")    
 - Security_groups_rules
 ![ALT HERE!](/images/ter_40.png "Security Groups Rules Resources")   
 
7. Kemudian coba cek state information menggunakan perintah `$ terraform show`.   
![ALT HERE!](/images/ter_41.png "Terraform state information")

8. Kemudian setelah selesai menggunakan resource dan ingin menghapus, gunakan perintah `$ terraform destroy`.   
![ALT HERE!](/images/ter_43.png "Terraform destroy")

## Referensi
1. [Terraform documentation](https://www.terraform.io/docs/index.html)
2. [Download terraform](https://releases.hashicorp.com/terraform/)
3. [Alicloud documentation](https://www.alibabacloud.com/help)
4. [Terraform alicloud provider](https://www.terraform.io/docs/providers/alicloud/index.html)
