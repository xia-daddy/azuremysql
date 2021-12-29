# Azure Database for Mysql
-----   
## 준비사항   
+ [Mysqlworkbench download](https://dev.mysql.com/downloads/workbench/)   
  + [visual C++ redistributable for visual studio download](https://support.microsoft.com/ko-kr/help/2977003/the-latest-supported-visual-c-downloads) 

## 목차   

Lab01. Azure Database for Mysql 배포   

Lab02. 방화벽 설정 및 네트워크와 Service 엔드포인트 연결   

Lab03. 접속 테스트 및 환경 변수 변경   

Lab04. 특정 시점 복원   

Lab05. 모니터링   

Lab06. Repilcation   

## Lab01. Azure Database for Mysql 배포

### 단일 서버 배포 선택

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




