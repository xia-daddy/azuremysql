# Azure Database for Mysql
-----   
## 준비사항   
+ [Mysqlworkbench download](https://dev.mysql.com/downloads/workbench/)   
  + [visual C++ redistributable for visual studio download](https://support.microsoft.com/ko-kr/help/2977003/the-latest-supported-visual-c-downloads) 

## 제약사항

mysql이 지원하는 많은 storage engine 중 지원하는 engine은 아래 2가지 engine만 지원하고 MyISAM등의 엔진은 지원하지 않음.

+ InnoDB
+ MEMORY

이외의 제약사항은 아래 docs 참조

docs : <https://docs.microsoft.com/ko-kr/azure/mysql/concepts-limits>

## 목차   

1. [Azure Database for Mysql provisioning](#01)   
2. [Firewall setting and connect existing network to Service endpoint](#02)   
3. [Configure the connection and parameter variable change](#03)   
4. [Restore](#04)   
5. [Monitoring](#05)   
6. [Scale Up-Down Azure Database for MySQL](#06)   
7. [Repilcation](#07)  
8. [Migration](#08)

-----

## lab01. Azure Database for Mysql provisioning<a name="01"></a>

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
   
## Lab02. Firewall setting and connect existing network to Service endpoint<a name="02"></a>

### 방화벽 규칙 추가   

1. [DB리소스로 이동]   
2. [설정 > 연결보안]   
3. [방화벽 규칙 현재 클라이언트 IP 주소 추가 > 저장]   

![lab2_img01](https://user-images.githubusercontent.com/88179727/147713343-40bb1028-387c-4e40-b581-40526e65a863.png)   

### 기존 가상네트워크와의 연결   

1. [DB리소스로 이동]   
2. [설정 > 연결보안]   
3. [VNET 규칙 > 기존 가상 네트워크 추가]
+ 기존 vnet이 없다면 새 가상 네트워크 만들기로 생성하여 진행해 볼 수 있습니다.    

![lab2_img02](https://user-images.githubusercontent.com/88179727/147714245-da4fca90-f1cc-473d-9b65-3aa7f17793bf.png)   

4. [연결 정보 입력후 확인 클릭]   
5. [연결된 vnet 확인]   

docs: <https://docs.microsoft.com/ko-kr/azure/mysql/concepts-data-access-and-security-vnet>   

+ Azure Database for MySQL은 기본적으로는 DNS 통신을 하며 방화벽으로 핸들링 됩니다.   
+ 기존 서비스와는 service endpoint를 통하여 서브넷간의 통신을 할 수 있고, Public ip로 통신도 가능 합니다.   

Service Endpoint Enabled (Vnet-subnet 가동 중지 시간 발생) : <https://docs.microsoft.com/ko-kr/azure/virtual-network/virtual-network-service-endpoints-overview#considerations>  
VNET service Endpoint : <https://docs.microsoft.com/ko-kr/azure/virtual-network/virtual-network-service-endpoints-overview>

### Private link

docs: <docs : https://docs.microsoft.com/ko-kr/azure/mysql/concepts-data-access-security-private-link>   

1. [보안 > 프라이빗 엔드포인트 연결]
2. [ + 프라이빗 엔드포인트]

![lab2_img03](https://user-images.githubusercontent.com/88179727/147807423-1df2c12c-c908-4a57-b452-388b39529363.png)

3. [프라이빗 엔드포인트 기본사항 입력 > 다음]

|설정|값|
|:---|:---|
|프로젝트 세부 정보| |	
|Subscription|	구독을 선택합니다.|
|Resource group|	myResourceGroup 을 선택합니다. 이전 섹션에서 만든 것입니다.|
|인스턴스 세부 정보| |	
|Name|	myPrivateEndpoint 를 입력합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다.|
|지역|	지역 를 선택합니다.|

![lab2_img04](https://user-images.githubusercontent.com/88179727/147808029-8578bbab-c411-4c86-a456-b18f8050d7c7.png)

4. [생성할 엔드포인트 리소스 선택 > 다음] 

|설정|값|
|:---|:---|
|연결 방법|내 디렉터리의 Azure 리소스에 연결하도록 선택합니다.|
|Subscription|구독을 선택합니다.|
|리소스 유형|Microsoft DBforMySQL/servers 를 선택 합니다.|
|리소스|myServer 를 선택합니다.|
|대상 하위 리소스|Mysqlserver 를 선택 합니다.|


![lab2_img05](https://user-images.githubusercontent.com/88179727/147809043-3d15a537-0d51-4abc-b664-28dd62b9b4e6.png)

5. [가상 네트워크 및 서브넷 선택 > 다음]  

|설정|값|
|:---|:---|
|네트워킹| |	
|가상 네트워크|	MyVirtualNetwork 를 선택합니다.|
|서브넷|	mySubnet 을 선택합니다.|
|프라이빗 DNS 통합| |	
|프라이빗 DNS 영역과 통합|	아니오 를 선택합니다.|
|프라이빗 DNS 영역|	|

![lab2_img06](https://user-images.githubusercontent.com/88179727/147809484-9acb09d8-e8ad-4e91-9f27-1364300831e4.png)   


6. [검토 및 만들기 > 만들기]   

![lab2_img07](https://user-images.githubusercontent.com/88179727/147903783-b413b092-b7fe-43c8-add9-0a252206f688.png)   

7. [배포 진행 및 완료 확인]   

![lab2_img08](https://user-images.githubusercontent.com/88179727/147903898-ecb28c24-dc76-40d6-9512-ddce0f434058.png)   

8. [설정 > DNS구성 > Private IP 확인]

![lab2_img09](https://user-images.githubusercontent.com/88179727/147904212-9e940d13-dd6d-4372-9cda-32a6291486ad.png)   
   

+ Private Endpoint 구성도   

![lab2_img10](https://user-images.githubusercontent.com/88179727/147904612-777d7b71-523e-45bc-94b2-2c029304b779.png)   

-----

## Lab03. Configure the connection and parameter variable change<a name="03"></a>
   
### Workbench 를 이용하여 Mysql 접속

1. [mysql Resorce 로 이동 > 개요 > 서버이름 및 관리자 ID 확인]   

+ Azure Database for Mysql 단일서버는 아키텍처 특성상 관리자 ID 가 (유저명) + (@서버명) 으로 구성됨.

![lab3_img01](https://user-images.githubusercontent.com/88179727/147907754-9b0c82c4-6dc9-4f4d-a151-ea0c019d92a6.png)

2. [Workbench 를 싱행하여 접속 정보를 입력 후 연결]  
 
![lab3_img02](https://user-images.githubusercontent.com/88179727/147908222-1d80cdb4-bc5b-42e0-864b-35bf79a3da6e.png)

### Time zone 변경   

Azure Database for MySQL은 PaaS 서비스이기 때문에 로컬 서버의 시간을 변경할 수 없습니다.   
대신 timezone 변경으로 그와 동일하게 작업할 수 있습니다.   
MySQL Workbench를 통하여 하단 sql 스크립트를 실행 합니다.   

<pre>
<code>
SELECT NOW();

SET SQL_SAFE_UPDATES=0;
CALL mysql.az_load_timezone();
SET SQL_SAFE_UPDATES=1;

# 변경 할 time zone 조회
SELECT name 
FROM mysql.time_zone_name;

# session의 time zone 설정 
SET time_zone = 'Asia/Seoul';
SELECT NOW();

# mysql 재기동 이후에도 동일한 time_zone 설정을 원한다면 portal에서 설정해야 한다.
</code>
</pre>
   
### 서버 매개 변수 변경   

위에서 작업된 timezone 변경은 세션 수준이며 만일 MySQL Server가 재시작 된다면 다시 원래의 UTC로 돌아가게 됩니다.
이를 방지하기 위해서는 기존 On-prem 환경에서는 my.cnf에서 time_zone parameter 추가로 해결할 수 있지만 PaaS에서는 서버 매개 변수를 변경해야 합니다.

변경 불가능한 환경 변수도 있으며 이는 고정된 값으로 구성되어 있습니다. [서버매개변수 참고 docs](https://docs.microsoft.com/ko-kr/azure/mysql/concepts-server-parameters)

+ 버퍼풀 관련 (서버의 SKU에 의해 결정 됩니다.) [가격 책정 계층](https://docs.microsoft.com/ko-kr/azure/mysql/concepts-pricing-tiers)
+ innodb_flush_log_at_trx_commit : 1
+ sync_binlog : 1
+ innodb_log_file_size : 256MB
+ innodb_log_files_in_group : 2

일반적인 환경에서 필수로 변경해야 하는 환경 변수는 다음과 같습니다

+ timezone
+ character_set_server
+ collation_server
 
아래와 같이 Azure Portal을 통해 configuration을 변경할 수 있습니다.   

![lab3_img03](https://user-images.githubusercontent.com/88179727/147909595-c7cf8b37-034a-400d-a22a-b47a2866d84f.png)   
   
아래 Azure CLI 코드를 사용하여서도 간단하게 서버 레벨의 환경 변수를 변경 합니다.

<pre>
<code>
# 타임존 변경
az mysql server configuration set --name time_zone --resource-group $resourceGroup --server $mySQLName --value "Asia/Seoul"
# character_set_server 변경
az mysql server configuration set --name character_set_server --resource-group $resourceGroup --server $mySQLName --value "UTF8MB4"
# collation_server 변경
az mysql server configuration set --name collation_server --resource-group $resourceGroup --server $mySQLName --value "utf8mb4_unicode_ci"
</code>
</pre>

-----

## Lab04. Restore<a name="04"></a>

### 샘플데이터 생성   

Azure Database for MySQL에서는 다양한 방식 (Azure Portal, Azure CLI, Azure Powershell 등)으로 손쉽게 특정 시점으로 복원할 수 있습니다.
샘플 데이터베이스를 생성 후 특정 테이블을 실수로 삭제 한 뒤 삭제 이전 시점으로 복원하는 테스트를 진행 합니다.

MySQL Workbench를 실행한 후 샘플 데이터베이스를 전체 실행합니다.

-----
[샘플데이터DUMP](https://github.com/xia-daddy/azuremysql/blob/main/test.md)
-----


이후 테이블을 삭제하고 삭제된 시간을 따로 기록해 둡니다.

<pre>
<code>
SELECT UTC_TIMESTAMP();

DROP TABLE classicmodels.payments;
</code>
</pre>
   
+ 서버 매개 변수에서 Time_Zone 을 Aisa/Seoul 로 변경하였다고 하여 복구 시간을 Aisa/Seoul 로 생각해서는 안됩니다.
+ Database 내의 Time_Zone 을 변경한 것이지 서버의 Time_Zone 을 변경한 것이 아닙니다. 기본적으로 Azure Portal 은  UTC 시각을 기준으로 복구해야 합니다.

### 데이터베이스 복원

1. [Database 리소스의 개요 > 복원 클릭]

![lab4_img01](https://user-images.githubusercontent.com/88179727/147914416-1473dafe-7df1-437e-8253-25b170710f31.png)

2. [복원 시점 및 복원할 서버 이름 지정]

![lab4_img02](https://user-images.githubusercontent.com/88179727/147914638-4892f9e6-a576-4ded-b356-c3d734755398.png)

3. [복원된 DB로 접속하여 삭제된 데이터가 복구되었는지 확인]   

<pre>
<code>
SELECT * FROM classicmodels.payments;
</code>
</pre>

![lab4_img03](https://user-images.githubusercontent.com/88179727/147914998-6f2027e2-af3f-430d-992d-6cc64e1dbfb1.png)   


새로 복원된 서버는 방화벽의 정보는 가져오지만 설정한 Vnet 규칙은 가져오지 않습니다.
기존과 동일하게 신규 서버에 대한 Vnet Rule을 추가 합니다.

-----

## Lab05. Monitoring<a name="05"></a>

### Slow Query 모니터링 

1. [서버 매개 변수 설정에서 다음 설정을 변경]   
 
+ log_output : file
+ long_query_time : 1
+ slow_query_log : ON 

2. [MySQL Workbench에서 5초 이상 걸리는 쿼리를 사용]   

<pre>
<code>
SELECT /*+ MAX_EXECUTION_TIME(5000) */ 1 
FROM classicmodels.customers WHERE SLEEP(5);
</code>
</pre>

3. [서버 로그에서 생성된 로그 파일을 다운]   

![lab5_img01](https://user-images.githubusercontent.com/88179727/148010241-d4806280-8c0b-4c61-bf60-49ebc346f0df.png)

4. [다운로드 된 파일을 열어 슬로우쿼리가 잘 적재되었는지 확인]   

<pre>
<code>
c:\mysql\bin\mysqld.exe, Version: 5.7.32-log (MySQL Community Server (GPL)). started with:
TCP Port: 20173, Named Pipe: /tmp/mysql.sock
Time                 Id Command    Argument
# Time: 2022-01-04T04:32:42.901508Z
# User@Host: myadmin[myadmin] @  [xxx.xxx.xx.xx]  Id:    21
# Query_time: 5.000054  Lock_time: 0.140626 Rows_sent: 0  Rows_examined: 0
SET timestamp=1641270762;
SELECT /*+ MAX_EXECUTION_TIME(5000) */ 1 
FROM classicmodels.customers WHERE SLEEP(5)
LIMIT 0, 1000;
</code>
</pre>

### 서버 리소스 모니터링   

1. [모니터링 > 메트릭 > 메트릭 추가]   

![lab5_img02](https://user-images.githubusercontent.com/88179727/148012191-de021573-9a65-4a51-83ad-d0ad142f3617.png)

2. [메트릭 선택에서 확인할 메트릭 정보를 선택]

![lab5_img03](https://user-images.githubusercontent.com/88179727/148012417-14c5acd4-bce9-4bc6-90fb-3c31d8985032.png)

+ 여러 메트릭 정보를 추가하려면 메트릭 추가를 반복 추가합니다.

3. [모니터링 할 시간대를 선택하여 확인 가능]   

![lab5_img04](https://user-images.githubusercontent.com/88179727/148012970-c2721399-16e6-4ce4-a9c2-088bed67a4b7.png)

### CPU 사용률
Cpu 사용량을 모니터링 하 고 데이터베이스가 CPU 리소스를 소모 하 고 있는지 여부를 모니터링 합니다. CPU 사용률이 90% 이상이 면 vCores의 수를 늘리거나 다음 가격 책정 계층으로 확장 하 여 계산을 확장 해야 합니다. CPU를 확장/축소할 때 처리량 또는 동시성이 예상 대로 작동 하는지 확인 합니다.

### 메모리
데이터베이스 서버에 사용할 수 있는 메모리 양은 vcores 수에 비례 합니다. 메모리가 작업에 충분 한지 확인 합니다. 응용 프로그램을 부하 테스트 하 여 읽기 및 쓰기 작업을 위한 메모리가 충분 한지 확인 합니다. 데이터베이스 메모리 소비가 정의 된 임계값을 초과 하는 경우가 자주 증가 하는 경우 vCores 이상 성능 계층을 증가 시켜 인스턴스를 업그레이드 해야 함을 나타냅니다. 쿼리 저장소, 쿼리 성능 권장 사항을 사용 하 여 가장 긴 기간의 쿼리를 식별 합니다. 최적화할 수 있는 기회를 살펴보세요.

### Storage
MySQL 서버에 대해 프로 비전 된 저장소의 양에 따라 서버에 대 한 IOPs가 결정 됩니다. 서비스에서 사용 하는 저장소에는 데이터베이스 파일, 트랜잭션 로그, 서버 로그 및 백업 스냅숏이 포함 됩니다. 사용 된 디스크 공간이 총 프로 비전 된 디스크 공간의 85%를 초과 하지 않도록 합니다. 이 경우에는 데이터베이스 서버에서 데이터를 삭제 하거나 보관 하 여 공간을 확보 해야 합니다.

### 네트워크 트래픽
네트워크 수신 처리량, 네트워크 전송 처리량 – 초당 메가바이트 단위에서 들어오고 나가는 네트워크 트래픽 속도입니다. 서버에 대 한 처리량 요구 사항을 평가 하 고 처리량이 예상 보다 낮은 경우 지속적으로 트래픽을 모니터링 해야 합니다.

### 데이터베이스 연결
데이터베이스 연결 -Azure Database for MySQL에 연결 된 클라이언트 세션 수는 선택한 SKU 크기에 대 한 연결 제한과 맞춰야 합니다.
   

|메트릭|	메트릭 표시 이름|	단위|	설명|
|:--|:--|:--|:--|
|cpu_percent|	CPU 백분율|	백분율|	사용 중인 CPU의 비율|
|memory_percent|	메모리 백분율|	백분율|	사용 중인 메모리의 비율|
|io_consumption_percent|	IO 백분율|	백분율|	사용 중인 IO의 비율 (기본 계층 서버에는 적용 되지 않음)|
|storage_percent|	스토리지 비율|	백분율|	서버의 최대값을 초과하여 사용된 스토리지의 비율|
|storage_used|	스토리지 사용됨|	바이트|	사용 중인 스토리지의 양 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|
|serverlog_storage_percent|	서버 로그 스토리지 비율|	백분율|	서버의 최대 서버 로그 스토리지에서 사용된 서버 로그 스토리지의 백분율입니다.|
|serverlog_storage_percent|	사용된 서버 로그 스토리지|	바이트|	서버 로그 스토리지에서 사용된 크기입니다.|
|serverlog_storage_limit|	서버 로그 스토리지 제한|	바이트|	이 서버에 대한 서버 로그 스토리지의 최대 크기입니다.|
|storage_limit|	스토리지 제한|	바이트|	이 서버의 최대 스토리지|
|active_connections|	활성 연결 수|	개수|	서버에 대한 활성 연결 수|
|connections_failed|	실패한 연결|	개수|	서버에 대해 실패한 연결 수|
|seconds_behind_master|	복제 지연 시간(초)|	개수|	원본 서버에 대해 복제본 서버가 지연 된 시간 (초)입니다. (기본 계층 서버에는 적용 되지 않음)|
|network_bytes_egress|	네트워크 아웃|	바이트|	활성 연결을 통한 네트워크 출력의 크기입니다.|
|network_bytes_ingress|	네트워크 인|	바이트|	활성 연결을 통한 네트워크 입력의 크기입니다.|
|backup_storage_used|	사용된 백업 스토리지|	바이트|	사용된 백업 스토리지 양. 이 메트릭은 서버에 대해 설정 된 백업 보존 기간에 따라 유지 되는 모든 전체 데이터베이스 백업, 차등 백업 및 로그 백업에서 사용 하는 저장소의 합계를 나타냅니다. 백업 빈도는 서비스에서 관리 되며 개념 문서에서 설명 합니다. 지역 중복 저장소의 경우 백업 저장소 사용량이 로컬 중복 저장소의 두 배가 됩니다.|

-----

## Lab06. Scale Up-Down Azure Database for MySQL<a name="06"></a>

1. [Azure Potal의 Mysql 리소스로 이동]   

2. [설정의 가격 책정 계층 선택]   

3. [드래그로 원하는 스펙을 알맞게 적용후 확인]

![lab6_img01](https://user-images.githubusercontent.com/88179727/148014948-85017596-1966-4c13-aa04-657116c94c1d.png)

+ Azrue Database for Mysql 의 최대 Sku
+ vCore : 64
+ Storage : 16 TB 
+ backup : 35 Day

4. [배포 완료후 개요에서 변경된 Mysql Sku 를 확인]   

+ Sclae up & down 시에는 기존에 연결된 싱크가 끊어집니다.    
+ vCore Sku 는 up & down 으로 조정할 수 있으나 Storage 는 down 이 불가 합니다.

-----

## Lab07. Replication<a name="07"></a>

Azure Database for MySQL은 읽기 복제를 지원 합니다. 원본 서버에서 최대 5개의 복제본으로 복제할 수 있습니다.   
복제본은 MySQL 엔진의 네이티브 이진 로그(binlog) 파일의 위치 기반 복제 기술을 사용하여 비동기식으로 업데이트됩니다.   
읽기 전용 복제본은 마스터 노드와 동일 지역에 생성할 수도 있으며 다른 데이터센터에 생성할 수도 있습니다.   
   
복제본은 일반 Azure Database for MySQL 서버와 비슷한 방식으로 관리하는 새로운 서버입니다. 읽기 복제본의 경우, vCore 및 스토리지에 프로비저닝된 컴퓨팅에 대한 비용이 GB/월 단위로 청구됩니다.

### 읽기 복제본을 사용하는 경우
읽기 복제본 기능은 읽기 작업이 많은 워크로드의 성능과 규모를 향상시키는 데 도움이 됩니다. 읽기 작업은 복제본에 격리 될 수 있지만 쓰기 작업은 원본으로 이동할 수 있습니다.   
   
일반적인 시나리오는 BI와 분석 워크로드가 보고를 위한 데이터 원본으로 읽기 복제본을 사용하도록 합니다.   
   
복제본은 읽기 전용 이므로 원본에서 쓰기 용량 부담을 직접 줄이지 않습니다. 이 기능은 쓰기 작업이 많은 워크로드를 대상으로 하지 않습니다.   
   
읽기 복제본 기능은 MySQL 동기식 복제를 사용합니다. 이 기능은 동기식 복제 시나리오를 위한 것이 아닙니다. 원본 및 복제본 간의 지연 시간이 측정 됩니다. 복제본의 데이터는 결국 원본에 있는 데이터와 일치 하게 됩니다. 이러한 지연 시간을 수용할 수 있는 워크로드에 이 기능을 사용합니다.   

### 읽기 복제본 만들기

1. [Azure Potal의 Mysql 리소스로 이동]   

2. [설정 > 복제 카테고리 상단 복제본 추가]   

![lab7_img01](https://user-images.githubusercontent.com/88179727/148032436-ebacd04b-95a8-41c7-90ae-7a173a15ccac.png)   

3. [생성할 복제본 이름과 지역 선택 > 확인]   

![lab7_img02](https://user-images.githubusercontent.com/88179727/148036806-1c708523-21f9-4cec-8df4-842d3280b401.png)

4. [생성후 활성화 된 복제본 확인]

![lab7_img03](https://user-images.githubusercontent.com/88179727/148038115-d863eeef-d5d3-494e-b0dc-a1efa1232a7d.png)

5. [복제본 서버에 접속하여 복제 상태 확인]   

<pre>
<code>
-- 데이터 복제 확인
select * from classicmodels.orders limit 10;

-- 복제상태 확인
show slave status;
</code>
</pre>

+ Slave_IO_Running와 Slave_SQL_Running이 모두 Yes,
+ Read_Master_Log_Pos와 Exec_Master_Log_Pos 값이 일치,
+ Last_Errno는 0, Last_Error는 공란, Seconds_Behind_Master이 0이면 정상 복제중   
   
   
6. [원본서버에서 데이터 조작후 복제서버 적용 확인]   

<pre>
<code>
-- 원본서버
create database testdb;
use testdb;
create table dummy_table (
x int,
y int);
insert into dummy_table (x, y) values (1, 2);
select * from dummy_table;

-- 복제서버
use testdb;
select * from dummy_table;
</code>
</pre>

-----

## Lab08. Migration<a name="08"></a>

### Azure DMS를 이용하여 데이터를 마이그레이션 하는 방법을 알아봅니다.   

### Azure DMS를 이용한 마이그레이션

1. [Azure 마켓플레이스의 DMS 리소스 생성으로 이동]   

2. [Mysql 타겟을 선택 > 선택]   
+ DMS 는 SQL, PostgreSQL, MySQL, MongoDB, RDS(SQL,PostgreSQL) 을 지원합니다.   
 
![lab8_img01](https://user-images.githubusercontent.com/88179727/149249485-8d63f712-98ad-406f-9462-9a8f416c04c1.png)

3. [리소스 그룹 선택 > 서비스 이름 입력 > 다음:네트워킹]   
+ 가격 계층의 Standard 는 오프라인 마이그레이션을 지원하고 Primium 은 온라인 마이그레이션을 지원

![lab8_img02](https://user-images.githubusercontent.com/88179727/149249872-9d358d5c-2be8-42a9-950d-fdbd7da37bf8.png)

4. [기존의 가상네트워크를 선택하거나 새로 생성 > 검토+만들기]

![lab8_img03](https://user-images.githubusercontent.com/88179727/149250105-6e4193fc-6f40-4544-bd33-1cdcc2eb5334.png)

5. [전체 Summay 를 확인 > 만들기]

![lab8_img04](https://user-images.githubusercontent.com/88179727/149250215-e2709a60-3a37-4683-8d97-5fff72087718.png)

6. [DMS 생성후 리소스 화면 이동 > 새 마이그레이션 프로젝트]

![lab8_img05](https://user-images.githubusercontent.com/88179727/149251726-7fa7a34e-6668-4e9d-b098-09c6f16bc77d.png)

7. [프로젝트 생성 정보 입력]

![lab8_img06](https://user-images.githubusercontent.com/88179727/149251783-03ff4c2f-8fa5-40a4-b0b7-2a98a5544c43.png)

8. [소스 데이터베이스 정보 입력 > 다음:대상 선택]   
+ 편의상 소스는 원래 생성해두었던 원본 DB를 대상으로 합니다.

![lab8_img07](https://user-images.githubusercontent.com/88179727/149251999-2b27e4c9-207e-4395-bd04-ee33e4757e8e.png)

9. [타겟 데이터베이스 정보 입력 > 다음:데이터베이스 선택]

+ 대상으로 지정할 데이터베이스가 필요합니다. LAB1 에서 배포한 것처럼 새로운 데이터베이스를 이번엔 유연한 서버로 구성해보세요.   

![lab8_img08](https://user-images.githubusercontent.com/88179727/149255044-213a6965-766d-478b-87c3-455a5cff01c5.png)

10. [마이그레이션 대상 데이터베이스 스키마를 선택 > 다음:테이블 선택]   

+ 제약사항: DMS 를 이용한 마이그레이션은 타겟DB에 원본과 동일한 스키마가 미리 생성되어 있어야 합니다.
+ 워크벤치를 사용해 추출이 가능하지만 편의상 아래에 추출한 스키마 구문을 이용해 타겟DB의 워크벤치에서 실행한 후 진행해 보세요.

-----

<details>
<summary>스키마DUMP 펼치기</summary>
<div markdown="1">

<pre>
<code>

CREATE DATABASE  IF NOT EXISTS `classicmodels` /*!40100 DEFAULT CHARACTER SET utf8mb4 */;
USE `classicmodels`;
-- MySQL dump 10.13  Distrib 8.0.26, for Win64 (x86_64)
--
-- Host: jh-test-my.mysql.database.azure.com    Database: classicmodels
-- ------------------------------------------------------
-- Server version	5.6.47.0

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!50503 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `customers`
--

DROP TABLE IF EXISTS `customers`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `customers` (
  `customerNumber` int(11) NOT NULL,
  `customerName` varchar(50) NOT NULL,
  `contactLastName` varchar(50) NOT NULL,
  `contactFirstName` varchar(50) NOT NULL,
  `phone` varchar(50) NOT NULL,
  `addressLine1` varchar(50) NOT NULL,
  `addressLine2` varchar(50) DEFAULT NULL,
  `city` varchar(50) NOT NULL,
  `state` varchar(50) DEFAULT NULL,
  `postalCode` varchar(15) DEFAULT NULL,
  `country` varchar(50) NOT NULL,
  `salesRepEmployeeNumber` int(11) DEFAULT NULL,
  `creditLimit` decimal(10,2) DEFAULT NULL,
  PRIMARY KEY (`customerNumber`),
  KEY `salesRepEmployeeNumber` (`salesRepEmployeeNumber`),
  CONSTRAINT `customers_ibfk_1` FOREIGN KEY (`salesRepEmployeeNumber`) REFERENCES `employees` (`employeeNumber`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `employees`
--

DROP TABLE IF EXISTS `employees`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `employees` (
  `employeeNumber` int(11) NOT NULL,
  `lastName` varchar(50) NOT NULL,
  `firstName` varchar(50) NOT NULL,
  `extension` varchar(10) NOT NULL,
  `email` varchar(100) NOT NULL,
  `officeCode` varchar(10) NOT NULL,
  `reportsTo` int(11) DEFAULT NULL,
  `jobTitle` varchar(50) NOT NULL,
  PRIMARY KEY (`employeeNumber`),
  KEY `reportsTo` (`reportsTo`),
  KEY `officeCode` (`officeCode`),
  CONSTRAINT `employees_ibfk_1` FOREIGN KEY (`reportsTo`) REFERENCES `employees` (`employeeNumber`),
  CONSTRAINT `employees_ibfk_2` FOREIGN KEY (`officeCode`) REFERENCES `offices` (`officeCode`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `offices`
--

DROP TABLE IF EXISTS `offices`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `offices` (
  `officeCode` varchar(10) NOT NULL,
  `city` varchar(50) NOT NULL,
  `phone` varchar(50) NOT NULL,
  `addressLine1` varchar(50) NOT NULL,
  `addressLine2` varchar(50) DEFAULT NULL,
  `state` varchar(50) DEFAULT NULL,
  `country` varchar(50) NOT NULL,
  `postalCode` varchar(15) NOT NULL,
  `territory` varchar(10) NOT NULL,
  PRIMARY KEY (`officeCode`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `orderdetails`
--

DROP TABLE IF EXISTS `orderdetails`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `orderdetails` (
  `orderNumber` int(11) NOT NULL,
  `productCode` varchar(15) NOT NULL,
  `quantityOrdered` int(11) NOT NULL,
  `priceEach` decimal(10,2) NOT NULL,
  `orderLineNumber` smallint(6) NOT NULL,
  PRIMARY KEY (`orderNumber`,`productCode`),
  KEY `productCode` (`productCode`),
  CONSTRAINT `orderdetails_ibfk_1` FOREIGN KEY (`orderNumber`) REFERENCES `orders` (`orderNumber`),
  CONSTRAINT `orderdetails_ibfk_2` FOREIGN KEY (`productCode`) REFERENCES `products` (`productCode`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `orders`
--

DROP TABLE IF EXISTS `orders`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `orders` (
  `orderNumber` int(11) NOT NULL,
  `orderDate` date NOT NULL,
  `requiredDate` date NOT NULL,
  `shippedDate` date DEFAULT NULL,
  `status` varchar(15) NOT NULL,
  `comments` text,
  `customerNumber` int(11) NOT NULL,
  PRIMARY KEY (`orderNumber`),
  KEY `customerNumber` (`customerNumber`),
  CONSTRAINT `orders_ibfk_1` FOREIGN KEY (`customerNumber`) REFERENCES `customers` (`customerNumber`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `payments`
--

DROP TABLE IF EXISTS `payments`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `payments` (
  `customerNumber` int(11) NOT NULL,
  `checkNumber` varchar(50) NOT NULL,
  `paymentDate` date NOT NULL,
  `amount` decimal(10,2) NOT NULL,
  PRIMARY KEY (`customerNumber`,`checkNumber`),
  CONSTRAINT `payments_ibfk_1` FOREIGN KEY (`customerNumber`) REFERENCES `customers` (`customerNumber`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `productlines`
--

DROP TABLE IF EXISTS `productlines`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `productlines` (
  `productLine` varchar(50) NOT NULL,
  `textDescription` varchar(4000) DEFAULT NULL,
  `htmlDescription` mediumtext,
  `image` mediumblob,
  PRIMARY KEY (`productLine`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `products`
--

DROP TABLE IF EXISTS `products`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `products` (
  `productCode` varchar(15) NOT NULL,
  `productName` varchar(70) NOT NULL,
  `productLine` varchar(50) NOT NULL,
  `productScale` varchar(10) NOT NULL,
  `productVendor` varchar(50) NOT NULL,
  `productDescription` text NOT NULL,
  `quantityInStock` smallint(6) NOT NULL,
  `buyPrice` decimal(10,2) NOT NULL,
  `MSRP` decimal(10,2) NOT NULL,
  PRIMARY KEY (`productCode`),
  KEY `productLine` (`productLine`),
  CONSTRAINT `products_ibfk_1` FOREIGN KEY (`productLine`) REFERENCES `productlines` (`productLine`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;



</code>
</pre>  

</div>
</details>

-----

![lab8_img09](https://user-images.githubusercontent.com/88179727/149264795-8140e179-346f-4464-889d-519e21ed20b3.png)

11. [필요한 테이블을 선택 > 다음:마이그레이션 설정 구성 > 다음: 요약]   
+ 세부 설정을 위한 고급 마이그레이션 설정이 있으나 본 LAB 에서는 패스 합니다.

![lab8_img10](https://user-images.githubusercontent.com/88179727/149264982-0bebafa7-75f2-421d-8457-4a65a0c986b4.png)

12. [작업 이름을 지정한 후 마이그레이션 시작]   

![lab8_img11](https://user-images.githubusercontent.com/88179727/149265352-db094d56-e060-4efd-8d18-2b39c2ff9ebd.png)

13. [마이그레이션 완료 확인]   
+ 상태 값이 변화하면서 완료됨 으로 나오면 마이그레이션이 정상적으로 수행된 것입니다. 타겟 DB에서 데이터가 정상적으로 들어왔는지 확인합니다.

![lab8_img12](https://user-images.githubusercontent.com/88179727/149266038-6466bd06-d752-41bd-9f67-a606bc093903.png)
 





