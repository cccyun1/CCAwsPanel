第一步 获取子网ID跟VPCid
aws ec2 describe-instances --instance-ids <输入实例的id> --query 'Reservations[].Instances[].{VpcId:VpcId, SubnetId:SubnetId}' --output table
第二步 给VPC添加ipv6的cidr
aws ec2 associate-vpc-cidr-block --vpc-id <vpc-id> --amazon-provided-ipv6-cidr-block
第三步 查询IPV6块
aws ec2 describe-vpcs --vpc-ids vpc-0ec30739db1cf48fa --query 'Vpcs[].Ipv6CidrBlockAssociationSet[]' 
vpc-cidr-assoc-01a05b545dfd5ad56        2406:da14:c93:b400::/56 Amazon  ap-northeast-1
IPV6CIDRBLOCKSTATE      associated
vpc-cidr-assoc-031018f5317a67f26        2406:da14:84a:f500::/56 Amazon  ap-northeast-1
IPV6CIDRBLOCKSTATE      associated
第四步 为子网分配一个IPV6的地址
aws ec2 associate-subnet-cidr-block --subnet-id subnet-05eff8edaca7a0d89 --ipv6-cidr-block 2406:da14:c93:b411::/64

第五步 把已经创建好ipv6的子网分配到ec2实例中并启用ipv6地址然后重启一下机器
aws ec2 modify-subnet-attribute \
  --subnet-id <Subnet ID> \
  --map-public-ip-on-launch

第六步联网
1.获取路由表ID
aws ec2 describe-route-tables --query 'RouteTables[*].RouteTableId'
2.获取公网网关
aws ec2 describe-internet-gateways --query 'InternetGateways[*].InternetGatewayId'
3.编辑路由表并添加公网网关,路由目标是::/0,第二目标是Internet Gateway
aws ec2 create-route --route-table-id <route-table-id> --destination-ipv6-cidr-block ::/0 --gateway-id <gateway-id>
打通EC2的防火墙
获取安全组ID
aws ec2 describe-instances --instance-ids <instance-id> --query 'Reservations[].Instances[].SecurityGroups[].GroupId'
开启IPV6出入站防火墙
aws ec2 authorize-security-group-ingress --group-id <security-group-id> --ip-permissions '[{"IpProtocol": "-1", "Ipv6Ranges": [{"CidrIpv6": "::/0"}]}]'
aws ec2 authorize-security-group-egress --group-id <security-group-id> --ip-permissions '[{"IpProtocol": "-1", "Ipv6Ranges": [{"CidrIpv6": "::/0"}]}]'

查询 EC2 实例的网络接口 ID
aws ec2 describe-instances --instance-ids <instance-id> --query "Reservations[].Instances[].NetworkInterfaces[].NetworkInterfaceId" --output text

给实例分配一个IPv6地址
aws ec2 assign-ipv6-addresses --network-interface-id <network-interface-id> --ipv6-address-count 1


完事儿了




