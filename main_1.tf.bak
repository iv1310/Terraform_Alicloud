provider "alicloud"{
}

resource "alicloud_instance" "example-ecs"{
	instance_type="ecs.g5.large"
	image_id="ubuntu_16_04_64_20G_alibase_20190513.vhd"
	security_groups=["sg-k1agjazrdjzag37rx3ab"]
	vswitch_id="vsw-k1a38rwbjbm1hk7z53cem"
}
