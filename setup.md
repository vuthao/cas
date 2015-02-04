### LDAP là gì?

Chắc ai cũng biết CSDL là gì rồi. LDAP (Lightweight directory access protocol - giao thức truy cập thư mục hạng nhẹ) cũng được dùng để lưu trữ dữ liệu giống CSDL, nhưng khác ở chỗ là dữ liệu được lưu trữ trong LDAP đa phần chỉ dùng để đọc, và hiếm khi bị chỉnh sửa. Gọi nó là directory bởi vì dữ liệu được lưu theo cấu trúc phân cấp (tree-like), mang lại tốc độ truy xuất cực nhanh. Đây là một trong những sự khác biệt chủ yếu giữa CSDL và LDAP.
Bản thân LDAP là tên một giao thức, còn công cụ cụ thể thì có thể do nhiều nhà phát hành khác nhau sản xuất: Microsoft Active Directory, Apache Directory, v.v

Để tổng quát hóa, từ nay mình gọi LDAP và CSDL là Tầng Lưu Trữ.

###  CAS là gì?

Central Authenticate Service (dịch vụ chứng thực trung tâm) là một dịch vụ được ủy thác để quản lý việc đăng nhập / đăng xuất của người dùng. Các thư viện hỗ trợ triển khai CAS gồm có: OpenSSO, CAS, v.v.

Từ nay, mình sẽ gọi các dịch vụ CAS là Tầng Xác Thực

###  Liferay là gì?

Liferay là gì thì tự bạn cũng tìm hiểu được. Từ nay mình xếp nó vào Tầng Ứng Dụng

### Cơ chế hoạt động

Trong Tầng Ứng Dụng bao gồm nhiều ứng dụng cần người dùng phải đăng nhập, vd: Liferay portal, HVA website, facebook. Đuối nhất là mỗi lần truy cập vào ứng dụng nào đều phải làm thao tác đăng nhập. Do đó mấy người lười mới họp nhau lại để sinh ra khái niệm Single Sign-on - Đăng nhập một lần và có thể truy xuất tất cả các ứng dụng trong hệ thống đó.

Tất nhiên trong mỗi ứng dụng đều chứa thông tin cá nhân và thông tin đăng nhập của người dùng, và quan trọng nhất là thông tin phân quyền. Nói cách khác, ứng dụng đó vẫn hoạt động như bình thường, ngoại trừ việc nó không phụ trách khâu đăng nhập/đăng xuất nữa.

Người dùng có thể đứng tại bất kỳ ứng dụng nào của Tầng Ứng Dụng, vd Liferay, nhấn nút Login, họ sẽ được chuyển sang trang đăng nhập của Tầng Xác Thực. Sau khi nhập username, password, Tầng Xác Thực sẽ truy xuất xuống Tầng Lưu Trữ để kiểm tra thông tin, cái này là chuyện quá bình thường rồi. Nếu username và pass hợp lệ, người dùng sẽ được redirect trở lại trang Liferay, đồng thời Tầng Xác Thực sẽ nói với Liferay là "thằng cha có username này đã đăng nhập rồi nha ku". Lúc này Liferay sẽ công nhận là user đó đã đăng nhập trên trang của mình, và dựa vào bảng phân quyền trong CSDL của chính nó để đối xử với user này như .... bình thường.

Nếu sau đó người dùng (cũng ở trên trình duyệt đó) truy cập sang HVA hay facebook, việc đầu tiên mà ứng dụng đó làm là hỏi Tầng Xác Thực xem thằng cha này đã đăng nhập chưa, nếu chưa thì redirect qua trang đăng nhập của Tầng Xác Thực. Nếu rồi thì Tầng Xác thực sẽ gửi cái username cho ứng dụng đó, ứng dụng đó sẽ công nhận rằng user này đã đăng nhập, và load dữ liệu liên quan đến username này như.... bình thường.

Khi user nhấn nút logout tại bất kỳ ứng dụng nào, họ sẽ được chuyển đến trang Logout successful của Tầng Xác Thực. Hoặc khi đóng trình duyệt, người đó được coi là đã log-out.

### Những điểm cần lưu ý:

- Username dùng để đăng nhập trên Tầng Xác Thực nếu tồn tại trong csdl của ứng dụng nào, thì ứng dụng đó mới công nhận username đó đã đăng nhập trên trang mình. Cho nên các ứng dụng hỗ trợ single sign-on sẽ có cơ chế đề đồng bộ hóa dữ liệu đăng nhập của mình với Tầng Xác Thực. Cụ thể là Liferay, vào Control Panel > Portal Settings > CAS > Enable CAS lên, check vào Export from LDAP để khi nào một user đc tạo hoặc chỉnh sửa thông tin trên LDAP sẽ được đồng bộ vào Liferay. Rồi vào Portal Settings > LDAP > Enable LDAP lên, không check Required, check vào Export để khi nào một user đc tạo hoặc chỉnh sửa thông tin trên Liferay sẽ được đồng bộ với LDAP.

- Khi username và password bị đánh cắp có nghĩa là "mất một lần là mất tất cả". Vì kẻ trộm có thể truy xuất vào mọi nơi trong hệ thống.

- Server của Tầng Xác Thực bị down, "một con ngựa đau, cả tàu nhịn đói", người dùng sẽ không thể đăng nhập vào bất kỳ ứng dụng nào. May thay, Liferay có cơ chế là người dùng với quyền admin có thể đăng nhập bằng csdl của Liferay.

- Có nhiều cách để CAS xác thực người dùng chứ không chỉ có username/password (vd: X509 Certificate,...)


## 1) Download Java JDK / JRE
http://www.oracle.com/technetwork/java/javase/downloads/index.html
Sử dụng Java JDK / JRE 7
http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html

Sử dụng bản  jdk-7u75-linux-x64.rpm
Upload lên server để cài đặt
```
rpm  -Uvh jdk-7u75-linux-x64.rpm
```
Kiểm tra: 
```
java -version
```

##  2) Install Tomcat 7
Step 1: download tomcat

```
cd /usr/share
wget http://mirrors.digipower.vn/apache/tomcat/tomcat-7/v7.0.57/bin/apache-tomcat-7.0.57.tar.gz
tar -xzf apache-tomcat-7.0.57.tar.gz
rm apache-tomcat-7.0.57.tar.gz -f
cd /etc/init.d

vi tomcat
```

In this new file copy the following:

```
#!/bin/bash
# description: Tomcat Start Stop Restart
# processname: tomcat
# chkconfig: 234 20 80

JAVA_HOME=/usr/java/jdk1.7.0_75
export JAVA_HOME
PATH=$JAVA_HOME/bin:$PATH
export PATH
CATALINA_HOME=/usr/share/apache-tomcat-7.0.57

case $1 in
start)
sh $CATALINA_HOME/bin/startup.sh
;;
stop)
sh $CATALINA_HOME/bin/shutdown.sh
;;
restart)
sh $CATALINA_HOME/bin/shutdown.sh
sh $CATALINA_HOME/bin/startup.sh
;;
esac
exit 0
```
Step 2: Save this and run the following command
```
chmod 755 tomcat
chkconfig --add tomcat
chkconfig --level 234 tomcat on
```

Step 4: Allowing Port 80 through firewall and redirecting 8080 to port 80
```
iptables -I INPUT 1 -p tcp --dport 8080 -j ACCEPT
iptables -I INPUT 1 -p tcp --dport 80 -j ACCEPT
iptables -t nat -A PREROUTING -p tcp -m tcp --dport 80 -j REDIRECT --to-ports 8080
iptables -t nat -A PREROUTING -p udp -m udp --dport 80 -j REDIRECT --to-ports 8080
service iptables save
service iptables restart
```

Step 4: You can now start, stop and restart tomcat using:
```
service tomcat stop
service tomcat start
service tomcat restart
```
Step 5: Run Tomcat
http://ip

http://ip:8080


## CAS Server 3.5.2 Release

donload https://www.apereo.org/cas/download

Step 1. copy modules/cas-server-webapp-VERSION.war to Tomcat's webapps/ directory

(CATALINA_HOME=/usr/share/apache-tomcat-7.0.57/webapps)

Rename the WAR into cas.war.

Step 2. restart tomcat
```
service tomcat restart
```

http://10.0.0.17/cas/login


# Easy Steps to Enable SSL / HTTPS on Tomcat Server
If you are running tomcat server that runs only on HTTP, follow the 2 easy steps mentioned below, to configure tomcat for SSL.
1. Create Keystore using Java keytool

First use the keytool to create a java keystore as shown below. Make sure to note down the password that you enter while creating the keystore.
$JAVA_HOME/bin/keytool -genkey -alias tomcat -keyalg RSA
```
/usr/java/jdk1.7.0_75/bin/keytool -genkey -alias tomcat -keyalg RSA
```
(Khi cài đặt nhớ tắt bộ gõ tiếng việt)

Enter keystore password: HXetjOy6TeSeVsS
```
What is your first and last name?
  [Unknown]:  Vu
What is the name of your organizational unit?
  [Unknown]:  Vinades
What is the name of your organization?
  [Unknown]:  NukeViet
What is the name of your City or Locality?
  [Unknown]:  HaNoi
What is the name of your State or Province?
  [Unknown]:  ThanhXuan
What is the two-letter country code for this unit?
  [Unknown]:  vn
Is CN=Vu, OU=Vinades, O=NukeViet, L=HaNoi, ST=ThanhXuan, C=vn correct?
  [no]:  y

Enter key password for <tomcat>
	(RETURN if same as keystore password):
Re-enter new password:
```

This will create the .keystore file under the /root home directory as shown below.

# ls -l /root/.keystore
-rw-r--r-- 1 root root 1391 Apr  6 11:19 .keystore

2. Modify the server.xml file

Locate the /usr/share/apache-tomcat-7.0.57/conf/server.xml file located under the tomcat directory. If the Connector port=”8443″ is commented out, you should uncomment it first. Please note that the comments in the server.xml file are enclosed in <!– and –> as shown below. You should remove the 1st and last line from the following code snippet.

# vi server.xml
```
   <!--
   <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true"
              maxThreads="150" scheme="https" secure="true"
              clientAuth="false" sslProtocol="TLS" />
   -->
```

Now, add the keystore information to the server.xml as shown below. Replace the your-key-password with the password you provided in the step 1 while creating the keystore.
```
   <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true"
              maxThreads="150" scheme="https" secure="true"
              keystoreFile="/root/.keystore" keystorePass="your-key-password"
              clientAuth="false" sslProtocol="TLS" />
```

Step 4: Allowing Port 443 through firewall and redirecting 8443 to port  443
```
iptables -I INPUT 1 -p tcp --dport 8443 -j ACCEPT
iptables -I INPUT 1 -p tcp --dport 443 -j ACCEPT
iptables -t nat -A PREROUTING -p tcp -m tcp --dport 443 -j REDIRECT --to-ports 8443
iptables -t nat -A PREROUTING -p udp -m udp --dport 443 -j REDIRECT --to-ports 8443

service iptables save
service iptables restart
service tomcat restart

```
Finally, restart the tomcat server and access the application using https://{your-ip-address}:8443/

## CAS LDAP
```
iptables -I INPUT 1 -p tcp --dport 389 -j ACCEPT
service iptables save
service iptables restart
```
