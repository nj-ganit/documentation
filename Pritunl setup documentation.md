# Installing Pritunl VPN in EC2 instance

<p>
<img src="https://pritunl.com/img/pritunl_onelogin.png" alt="drawing" width="200"/ align="left"></p>
</br>

#### Pritunl Enterprise VPN Server</br>
</br>

To install Pritunl on AWS open the create instance interface and search for the Oracle Linux owner ID 131827586825 then select the Community AMIs tab. Select the latest Oracle Linux 8 AMI currently OL8.5-x86_64-HVM-2021-11-24. This will use the free official Oracle Linux 8 image with SELinux support. To find the latest release number check the Oracle Linux ISO Repository The left column will show a number such as 8.5, then find this number with the latest date in the AMI server results.

#### 1. Create a EC2 instance with OL8.5-x86_64-HVM-2021-11-24 AMI

Create security groups with required inbound access and make sure to keep the pem file downloaded.


[![ec2-pritunl.png](https://i.postimg.cc/NM0hCMJp/ec2-pritunl.png)](https://postimg.cc/xkBFc0Sz)



#### 2. Connect with EC2 instance using ssh. You can also use `Putty` to ssh into the instance.
###### Note: You need to convert pem file to ppk file (use puttyGen to convert) if you are using putty. 

#### 3. After creating the EC2 instance SSH to the server with the username ec2-user and run the commands below to install Pritunl and MongoDB.

```
sudo tee /etc/yum.repos.d/mongodb-org-6.0.repo << EOF
[mongodb-org-6.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/8/mongodb-org/6.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-6.0.asc
EOF
```

```
sudo tee /etc/yum.repos.d/pritunl.repo << EOF
[pritunl]
name=Pritunl Repository
baseurl=https://repo.pritunl.com/stable/yum/oraclelinux/8/
gpgcheck=1
gpgkey=https://repo.pritunl.com/stable/yum/oraclelinux/8/RPM-GPG-KEY-Pritunl
enabled=1
EOF
```

```
sudo yum -y install oracle-epel-release-el8
```

```
sudo yum-config-manager --enable ol8_developer_EPEL
```

```
sudo yum -y update
```

##### WireGuard server support</br>

```
sudo yum -y install wireguard-tool
```

```
sudo yum -y remove iptables-services
```

```
sudo systemctl stop firewalld.service
```

```
sudo systemctl disable firewalld.service
```

##### Import signing key from keyserver </br>
```
gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys 7568D9BB55FF9E5287D586017AE645C0CF8E292A
```

```
gpg --armor --export 7568D9BB55FF9E5287D586017AE645C0CF8E292A > key.tmp; sudo rpm --import key.tmp; rm -f key.tmp
```

##### Alternative import from download if keyserver offline </br>
```
sudo rpm --import https://raw.githubusercontent.com/pritunl/pgp/master/pritunl_repo_pub.asc
```

##### Install updated openvpn package from pritunl</br>
```
sudo yum --allowerasing install pritunl-openvpn
```

```
sudo yum -y install pritunl mongodb-org
```

```
sudo systemctl enable mongod pritunl
```

```
sudo systemctl start mongod pritunl
```

#### 4. The Pritunl VPN server is configured via the web interface accessed using the URL `<https://IP_Address>`. But first, obtain the authentication key to the MongoDB database.

[![Untitled-design-7.png](https://i.postimg.cc/05z58kxL/Untitled-design-7.png)](https://postimg.cc/dDKc6KhB)

#### 5. Run `sudo pritunl setup-key` command in the terminal and copy paste the key here. Click on save.

[![Untitled-design-4.png](https://i.postimg.cc/25hjrghd/Untitled-design-4.png)](https://postimg.cc/Jy7CQ6Tt)

#### 6. Run `sudo pritunl default-password` command in the terminal and copy paste the username and password here. Click on save.

[![Untitled-design-8.png](https://i.postimg.cc/4NR7K7WD/Untitled-design-8.png)](https://postimg.cc/Z0jK2RtH)

#### Once successfully authenticated, make the Pritunl initial settings that include changing the password and the IP address of the Pritunl server.

[![Untitled-design-9.png](https://i.postimg.cc/76L1WCFs/Untitled-design-9.png)](https://postimg.cc/ns6QsLcB)

#### Now you will granted with dashboard and you can add users, servers, and organizations.

#### After adding users, servers and organization you can downlaod the Pritunl client and add profile using users .tar file and start using Pritunl VPN. 
