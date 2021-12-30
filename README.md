# Azure Database for Mysql
-----   
## 준비사항   
+ [Mysqlworkbench download](https://dev.mysql.com/downloads/workbench/)   
  + [visual C++ redistributable for visual studio download](https://support.microsoft.com/ko-kr/help/2977003/the-latest-supported-visual-c-downloads) 

## 목차   

1. [Azure Database for Mysql provisioning](#Lab01.-Azure-Database-for-Mysql-provisioning)   
2. [Firewall setting and connect existing network to Service endpoint](#Lab02.-Firewall-setting-and-connect-existing-network-to-Service-endpoint)   
3. [Access test and parameter variable change]
4. [Restore]
5. [Monitoring]
6. [Scaling]
7. [Repilcation]   

-----

## Lab01. Azure Database for Mysql provisioning

### 단일 서버 배포 방법을 알아봅니다.

1. [Azure Portal 접속]   
2. [리소스 만들기]   
3. [mysql을 검색 후 Azure Database for Mysql 만들기 선택]   
4. [단일서버 만들기]   

![lab1_img01](https://user-images.githubusercontent.com/88179727/147627688-d3f588f4-fd5c-4601-99ee-fd7089d795f2.png)   

5. [생성할 DB 정보 작성]   
6. [서버구성]   

![lab1_img02](https://user-images.githubusercontent.com/88179727/147628858-4644e0c5-7703-45a6-9bc8-c9ae0c28f833.png)

7. [SKU 선택 및 확인]   

![lab1_img03](https://user-images.githubusercontent.com/88179727/147628879-cd78f406-3f37-407b-860d-372558f95ff3.png)

8. [검토 및 만들기] - [만들기]   

![lab1_img04](https://user-images.githubusercontent.com/88179727/147628882-f6313c0d-c88e-4b8a-b456-c2000e2c601f.png)   

9. [배포 확인]

![lab1_img05](https://user-images.githubusercontent.com/88179727/147628883-1b019b3b-4747-47d7-bfe1-809751f1aad9.png)
![lab1_img06](https://user-images.githubusercontent.com/88179727/147628885-7eb1954b-9fe0-48ab-8fd0-a8ebb18cf724.png)   

10. [리소스로 이동을 클릭하여 리소스확인]

![lab1_img07](https://user-images.githubusercontent.com/88179727/147628887-f222fb23-5798-4b86-b237-c87c92c790f0.png)   

-----
   
## Lab02. Firewall setting and connect existing network to Service endpoint

### 방화벽 규칙 추가   

1. [DB리소스로 이동]   
2. [설정 > 연결보안]   
3. [방화벽 규칙 현재 클라이언트 IP 주소 추가 > 저장]   

![lab2_img01](https://user-images.githubusercontent.com/88179727/147713343-40bb1028-387c-4e40-b581-40526e65a863.png)   

### 기존 가상네트워크와의 연결   

1. [DB리소스로 이동]   
2. [설정 > 연결보안]   
3. [VNET 규칙 > 기존 가상 네트워크 추가]   

![lab2_img02](https://user-images.githubusercontent.com/88179727/147714245-da4fca90-f1cc-473d-9b65-3aa7f17793bf.png)   

4. [연결 정보 입력후 확인 클릭]   
5. [연결된 vnet 확인]   

docs: <https://docs.microsoft.com/ko-kr/azure/mysql/concepts-data-access-and-security-vnet>   

+ Azure Database for MySQL은 기본적으로는 DNS 통신을 하며 방화벽으로 핸들링 됩니다.   
+ 기존 서비스와는 service endpoint를 통하여 서브넷간의 통신을 할 수 있고, Public ip로 통신도 가능 합니다.   






