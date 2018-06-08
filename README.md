# 1. db 쿼리 작성 7문제
1. 테이블생성
    - user 테이블 생성
    ---
    Field|Type|Null|Key|Default|Extra
    -----|----|----|---|-------|-----
    name|varchar(20)|YES||NULL|
    id|int(11)|NO|PRI|NULL|
    address|varchar(20)|YES||NULL|
    ---
    - ```create table user (name varchar(20), id int, address varchar(20), primary key(id));```
1. insert
    - 본인 이름으로 레코드 추가
    ---
    id|name|address
    -----|----|----
    0|sangmin lee|0x122bacd
    
    ---
    - ```insert into user(id,name,address) values(0,'sangmin lee','0x122bacd');```
1. update
    - menu 테이블에 transaction_id 2번의 meat noodle 한개를 spicy meat noodle로 변경
    - ```update menu set name='spicy meat noodle' where id=4;```
1. alter
    - user 테이블 id컬럼에 auto_increment를 적용하기
    - ```alter table user change id id int auto_increment;```
1. join
	- user_transaction.id와 menu.transaction_id 가 같은 것끼리 묶어주는 join을 해봐라
	
| id | sender_address | receiver_address | amount | id | transaction_id | name              | price |
----|----------------|------------------|--------|----|----------------|-------------------|-------|
|  0 | 0x123          | 0xabc            |  13500 |  0 |              0 | meat noodle       |  6000 |
|  0 | 0x123          | 0xabc            |  13500 |  1 |              0 | spicy meat noodle |  7500 |
|  1 | 0x456          | 0xefg            |  75000 |  2 |              1 | spicy meat noodle |  7500 |
|  2 | 0x789          | 0xktkgg          |  12000 |  3 |              2 | meat noodle       |  6000 |
|  2 | 0x789          | 0xktkgg          |  12000 |  4 |              2 | spicy meat noodle |  6000 |

1. delete
    - 본인 이름의 레코드를 삭제하기
    - ```delete from user where name='김승훈';```
1. 트랜잭션을 이해하는가?  
    - 뒤로 돌리려면?(rollback)  
    - 트랜잭션 종료하려면? (commit)  
    - 트랜잭션으로 lock되도 가능한 명령은? (select)

# 2. Web 동작원리 5문제
1. php 소스 설명하기 (코딩을 제대로 한 경험이 있으면 처음보는 언어도 어느정도 의미는 해독이 가능함 아래 항목중에 3개 이상이면 ok)
    - 인자로 이름,이메일,엑세스 토큰을 받는다.
    - db에서 이름을 기준으로 select 한다.
    - db결과가 뭔가 하나라도 검색되면 access_token을 업데이트한다.
    - db결과가 없으면 회원가입한다.(db에 insert한다 도 ok)
    - 세션에 데이터들을 저장한다.
    
```php
function login($name,$email,$access_token,$expires_in)
{
	session_start();
	$modified_name="'".$name."'";
	$modified_email="'".$email."'";
	$modified_access_token="'".$access_token."'";

	$arr=array($modified_name,$modified_email,$modified_access_token,$expires_in);

	$parameters_string=implode(",",$arr);

	$query="select * from users where email='".$email."';";
	echo $query;
	$result=send_query($query);
	//로그인 시도
	if($result->num_rows!=0)
	{
		$query="update users set access_token='".$access_token."',access_token_expire_date=".$expires_in." where email='".$email."';";
		//echo $query;
		$result=send_query($query);
		//echo "naver_login result:".$result."<br>";
		$_SESSION["access_token"]=$access_token;
		$_SESSION["name"]=$name;
		$_SESSION["email"]=$email;

		if(!$result)
		{
			echo "login query false<br>\n";
			return false;
		}
		else
		{
			//echo "query true<br>\n";

			return true;
		}
	}

	//회원가입시도
	else
	{
		//btc address 생성
		echo "btc address create<br>\n";
		$btc_address=create_btc_address();
		echo "btc_address: ".$btc_address."<br>\n";
		//bch address 생성

		echo "bch address create<br>\n";
		$bch_address=create_bch_address("hello3");
		echo "bch_address: ".$bch_address."<br>\n";
		//etc address 생성

		echo "etc address create<br>\n";
		$etc_address=create_etc_address("123");
		//echo "etc_address: ".$etc_address."<br>\n";
		//eth address 생성

		echo "eth address create<br>\n";
		$eth_address=create_eth_address("123");
		//echo "eth_address: ".$eth_address."<br>\n";

		echo "xrp address create<br>\n";
		$xrp_address=create_xrp_address("123");
		echo "xrp_address: ".$xrp_address."<br>\n";

		
		$query="insert into users(name,email,access_token,access_token_expire_date,btc_address,bch_address,etc_address,eth_address,xrp_address) values(".$parameters_string.",'".$btc_address."','".$bch_address."','".$etc_address."','".$eth_address."','".$xrp_address."');";
		$result=send_query($query);


		$_SESSION["access_token"]=$access_token;
		$_SESSION["name"]=$name;
		$_SESSION["email"]=$email;

		if(!$result)
		{
			echo "signup query false<br>\n";
			return false;
		}
		else
		{
			return true;
		}


	}
}
``` 
1. post get 차이점
    - get은 url에 합쳐서 보내고 post는 body에 담아서 보낸다. 설명하면 ok
    - header에 content-type이라는 헤더필드에 데이터 타입이 명시된다까지 알면 가산점
1. 세션, 쿠키 설명
    - 세션은 서버에서 쿠키는 클라이언트에 설명하면 ok
    - 쿠키의 보안성에 대해서 설명하면 가산점
1. CURL 사용해본적 있는지?
    - 사용경험 있으면 들어볼것
1. json-rpc에 관해서 알고 있는지?
    - 사용경험 있으면 들어볼것

# 3. 사용자 인증기술 1문제
1. Oauth에 대해서 설명해보시오.

# 4. MVC 패턴 1문제
1. MVC 패턴을 설명하세요.
    - MVC 각각이 뭔지 알면 됨.
# 5. 리눅스 사용경험 1문제
1. 리눅스 사용 경험을 설명해보시오.
    - 그냥 보고 판단

# 6.블록체인 기초이론 4문제
1. 블록체인이란 무엇인가?
    - 각 블록의 헤시값이 다음 블록에 연결되는 형태라고 설명하면 ok
1. 가장유명한 코인이 어떤게 있는가?
    * bitcoin는 트렌젝션 기록을 보고 송신자를 알 수 있는가?
        - 직접적으로 알 수는 없음
    * ethereum의 가장 큰 특징은? 
        - smart contract 설명
1. 4차 산업혁명에 블록체인이 포함된다고 생각하는가? 만약 그렇다면 왜 그럴까? 아니라면 왜 그럴까? 자신의 신념이나 성향에 기반해서 설명하길

# 7. 개발경험 1문제(이력서 기반)



