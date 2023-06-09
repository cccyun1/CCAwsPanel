# AWS给已创建好的实例分配IPV6地址并启用
### 1.创建双堆栈 VPC

​```aws ec2 create-vpc --cidr-block 10.0.0.0/24 --amazon-provided-ipv6-cidr-block --query Vpc.VpcId --output text​```  
将返回的VPC id记录  
### 2.[双堆栈 VPC] 使用以下 describe-vpcs 命令获取与 VPC 关联的 IPv6 CIDR 块。
```aws ec2 describe-vpcs --vpc-id <输入刚刚获取的Vpcid> --query Vpcs[].Ipv6CidrBlockAssociationSet[].Ipv6CidrBlock --output text```  
### 3.创建双堆栈子网  
```aws ec2 create-subnet --vpc-id <输入刚刚获取的Vpcid> --cidr-block 10.0.1/0/20 --ipv6-cidr-block 2600:1f13:cfe:3600::/64<只更改3600后面两位数> --availability-zone us-east-2a<修改为实例所在地区> --query Subnet.SubnetId --output text```  
记录返回的子网id  
### 4.分配子网到对应的实例  
```aws ec2 modify-subnet-attribute --subnet-id <记录返回的子网id> --map-public-ip-on-launch```  
### 5.重启实例

输入ip -6 addr show检查是否分配IPV6地址
