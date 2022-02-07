## EC2-LAMP-ELB 구성하기

---

### 목표: Amazon EC2를 AMP 구축하는 방법, 인스턴스를 이미지로 변환 및 AMI에서 이미지를 이용한 인스턴스 생성, 로드벨런서를 이용한 애플리케이션 로드 벨런싱을 구축해본다.

---

### 용어정리

**EC2** : Amazon Elastic Compute Cloud로써 클라우드에서 확장 가능 컴퓨팅 용량을 제공합니다. Amazon EC2에서는 확장 또는 축소를 통해 요구 사항 변경 또는 사용량 스파이크를 처리할 수 있으므로 트래픽을 예측할 필요성이 줄어듭니다.

**VPC** : Amazon Virtual Private Cloud(Amazon VPC)를 이용하면 사용자가 정의한 가상 네트워크로 AWS 리소스를 시작할 수 있습니다. VPN과 비슷한 개념으로써 VPC 끼리 독립된 네트워크 처럼 작동합니다. VPC는 하나의 리전에 종속된다. 일반적으로 보안을위해 AWS 리소스간 허용을 최소화하고 그룹별로 손쉽게 네트워크를 구성하기위해 많이사용합니다.

**서브넷** : VPC의 내부를 쪼개는 것이라고 생각하면 된다. 더 많은 네트워크 망을 만들기 위해서 사용된다.

**로드벨런서** : 여러 대의 Server에게 균등하게 Traffic을 분산시켜주는 역할을 한다.

**로드 벨런싱 선택 기준**

Round Robin :

    단순히 Round Robin으로 분산하는 방식입니다.

Least Connections :

    연결 개수가 가장 적은 서버를 선택하는 방식입니다.
    트래픽으로 인해 세션이 길어지는 경우 권장하는 방식입니다.

Source :

    사용자의 IP를 Hashing하여 분배하는 방식입니다.
    사용자는 항상 같은 서버로 연결되는 것을 보장합니다.


---

![EC2-LAMP-ELB 구성하기1](https://raw.githubusercontent.com/C0deWave/aws_study/c639d685e521a1e604f9ec6f9759d2f140456564/이미지/EC2-LAMP-ELB%20구성하기1.png)

1. EC2에서 인스턴스 생성을 누른다.
2. 유형은 아마존 리눅스를 사용
3. 서브넷은 ap-northeast-2a
4. 고급 세부 정보에서 사용자 데이터에 스크립트 입력
5. 인터넷에 해당 아이피를 입력해서 잘 작동 되는지 확인 
6. AMI 생성하기
7. 생성된 AMI 이미지를 이용해서 2번째 인스턴스 생성
8. 서브넷은 2c로 지정하고 나머지는 동일하게 진행
9. EC2 - 로드벨런서 - Application Loadbalancer
10. Scheme는 외부 인터넷과 연결이기 때문에 Internat-facting 선택
11. 사용하는 VPC와 서브셋 선택 (2a, 2c)
12. 시큐리티 그룹 80포트 인바운드 지정 생성후 적용
13. 라우팅에서 타겟 그룹생성 및 인스턴스 등록
14. alb의 DNS Name을 이용해서 로드 밸런싱을 시작
15. 잘 작동 되는 것을 확인

같은 DNS Name을 이용해서 로드 벨런싱이 된 모습을 볼 수 있습니다.

![EC2-LAMP-ELB 구성하기2](https://raw.githubusercontent.com/C0deWave/aws_study/c639d685e521a1e604f9ec6f9759d2f140456564/이미지/EC2-LAMP-ELB%20구성하기2.png)

---

### 스크립트

#!/bin/bash

yum update -y
amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2

yum install -y httpd mariadb-server

systemctl start httpd

systemctl enable httpd

usermod -a -G apache ec2-user

chown -R ec2-user:apache /var/www

chmod 2775 /var/www

find /var/www -type d -exec chmod 2775 {} \;

find /var/www -type f -exec chmod 0664 {} \;

echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php

if [ ! -f /var/www/html/bootcamp-app.tar.gz ]; then

cd /var/www/html

wget https://s3.amazonaws.com/immersionday-labs/bootcamp-app.tar

tar xvf bootcamp-app.tar

chown apache:root /var/www/html/rds.conf.php

wget https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.tar.gz

mkdir phpMyAdmin && tar -xvzf phpMyAdmin-latest-all-languages.tar.gz -C phpMyAdmin --strip-components 1

cd /var/www/html/phpMyAdmin/

cp config.sample.inc.php config.inc.php

fi

---