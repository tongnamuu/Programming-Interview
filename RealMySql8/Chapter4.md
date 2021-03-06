# 04 아키텍처 #

MySQL 서버는 사람의 머리 역할을 담당하는 MySQL 엔진과 손발 역할을 담당하는 스토리지 엔진으로 구분할 수 있다. 스토리지 엔진은 핸들러 API를 만족하면 누구든지 스토리지 엔진을 구현해서 MySQL 서버에 추가해서 사용할 수 있다.

## 4.1 MySQL 엔진 아키텍처 ##

MySQL 서버는 크게 MySQL 엔진과 스토리지 엔진으로 구분할 수 있다.

### 4.1.1.1 MySQL 엔진 ###
MySQL 엔진은 클라이언트로부터의 접속 및 쿼리 요청을 처리하는 커넥션 핸들러와 SQL 파서 및 전처리기, 쿼리의 최적화된 실행을 위한 옵티마이저가 중심을 이룬다. 또한 mysql은 표준 sql 문법을 지원하기 때문에 표준 문법에 따라 작성된 쿼리는 타 DBMS와 호환되어 실행될 수 있다

### 4.1.1.2 스토리지 엔진 ###

MySQL 엔진은 요청된 SQL 문장을 분석하거나 최적화 하는 등 DBMS 의 두뇌에 해당하는 처리를 수행하고 실제 데이터를 디스크 스토리지에 저장하거나 디스크 스토리지로부터 데이터를 읽어오는 부분은 스토리지 엔진이 전담한다. MySQL 서버에서 MySQL 엔진은 하나이지만 MySQL 스토리지 엔진은 여러개를 사용할 수 있다. 각 스토리지 엔진은 성능향상을 위해 MyISAM 스토리지 엔진은 키 캐시, InnoDB 엔진은 InnoDB 버퍼풀같은 기능을 제공한다.

### 4.1.1.3 핸들러 API ###

MySQL 엔진의 쿼리 실행기에서 데이터를 쓰거나 읽어야 할 때는 각 스토리지 엔진에 쓰기 또는 읽기를 요청하는데, 이러한 요청을 핸들러 요청이라고 하고 여기서 사용되는 api를 핸들러 api 라고 한다. InnoDB 스토리지 엔진 또한 이 핸들러 API 를 이용해 MySQL 엔진과 데이터를 주고받는다. 이 핸들러 API를 통해 얼마나 많은 데이터 작업이 있었는지는 아래의 명령어로 확인이 가능하다.

``` SQL
SHOW GLOBAL STATUS LIKE 'Handler%'; 
```

### 4.1.2 MySQL 스레딩 구조 ###

MySQL 서버는 프로세스 기반이 아니라 스레드 기반으로 동작하며 크게 Foreground, background 서버로 구분할 수 있다. 백그라운드 스레드의 개수는 MySQL 서버의 설정에 따라 바뀔 수 있다. 동일한 이름의 스레드가 2개 이상인 경우는 여러 스레드가 동일 작업을 병렬로 수행하는 것이다.

### 4.1.2.1 포그라운드 스레드(클라이언트 스레드) ###

포그라운드 스레드는 MySQL 서버에 접속된 클라이언트의 수 만큼 존재하며 주로 각 클라이언트가 요청하는 쿼리 문장을 처리한다. 클라이언트 사용자가 작업을 마치고 커넥션을 종료하면 커넥션을 담당하던 스레드는 스레드 캐시로 돌아간다. 단 스레드 캐시가 일정 이상 차있다면 스레드 캐시에 넣지 않고 종료시킨다. 스레드 캐시에 유지할 수 있는 최대 스레드 개수는 thread_cache_size 시스템 변수로 설정한다.

포그라운드 스레드는 데이터를 MySQL 데이터 버퍼나 캐시로부터 가져오며 버퍼나 캐시에 없으면 직접 디스크의 데이터나 인덱스 파일로부터 데이터를 불러와서 작업을 처리한다. MyISAM 테이블은 디스크 쓰기 작업까지 포그라운드 스레드에서 처리한다. InnoDB 테이블은 데이터 버퍼나 캐시까지만 포그라운드에서 처리하고 버퍼로부터 디스크로 기록하는 작업은 백그라운드 스레드가 처리한다.

### 4.1.2.2 백그라운드 스레드 ###

InnoDB의 백그라운드 스레드는 아래와 같은 작업들을 처리한다.

- 인서트 버퍼를 병합하는 스레드
- 로그를 디스크로 기록하는 스레드
- InnnoDB 버퍼 풀의 데이터를 디스크에 기록하는 스레드
- 데이터를 버퍼로 읽어오는 스레드
- 잠금이나 데드락을 모니터링하는 스레드

MyISAM 의 경우에는 해당사항이 없다.

사용자의 요청을 처리할 때 쓰기 작업은 지연되어 처리될 수 있지만 데이터 읽기 작업은 절대 지연될 수 없다. 상용 DBMS는 모두 쓰기 작업을 버퍼링해서 일괄 처리하는 기능이 있고 InnoDB 또한 그렇게 처리한다. 하지만 MyISAM은 사용자 스레드가 쓰기 작업까지 함께 처리하도록 설계되어 있다.

### 4.1.3 메모리 할당 및 사용구조 ###

MySQL에서 사용되는 메모리 공간은 크게 글로벌 메모리 영역과 로컬 메모리 영역으로 나뉜다. 글로벌 메모리 영역은 MySQL 서버가 시작되면서 운영체제로부터 할당받는다.

### 4.1.3.1 글로벌 메모리 영역 ###

일반적으로 클라이언트 스레드의 수와 무관하게 하나의 메모리 공간만 할당된다. 단 필요에 따라 2개 이상의 메모리 공간을 할당받을 수도 있지만 클라이언트 스레드 수와는 무관하며 생성된 글로벌 영역이 여러개라고 해도 모든 스레드에 의해 공유된다.
대표적인 글로벌 메모리 영역은 다음과 같다.

- 테이블 캐시
- InnoDB 버퍼 풀
- InnoDB 어댑티드 해시 인덱스
- InnoDB 리두 로그 버퍼
- 바이너리 로그 버퍼
- MyISAM 키 캐시

### 4.1.3.2 로컬 메모리 영역 ###

세션 메모리 영역이라고도 하며 클라이언트 스레드가 쿼리를 처리하는데 사용하는 메모리 영역이다. 로컬 메모리는 각 클라이언트 스레드 별로 독립적으로 할당되며 공유되지 않는다.
대표적은 로컬 메모리 영역은 다음과 같다.

- 정렬 버퍼
- 조인 버퍼
- 바이너리 로그 캐시
- 네트워크 버퍼

### 4.1.4 플러그인 스토리지 엔진 모델 ###

MySQL  서버에서는 스토리지 엔진뿌만 아니라 다양한 기능을 플러그인 형태로 지원한다. 인증, 전문 검색 파서, 쿼리 재작성 같은 플러그 인이 있다. 뿐만 아니라 MySQL 서버의 기능을 커스텀하게 확장할 수 있게 플러그인 API가 매뉴얼에 공개되어 있다.

### 4.1.5 컴포넌트 ###

MySQL 8.0 부터는 기존의 플러그인 아키텍처를 대체하기 위해 컴포넌트 아키텍처가 지원된다. 서버의 플러그인은 다음과 같은 단점이 있다.

- 플러그인은 오직 MySQL 서버와 인터페이스할 수 있고 플러그인끼리는 통신할 수 없음
- 플러그인은 MySQL 서버의 변수나 함수를 직접 호출하기 때문에 안전하지 않음
- 플러그인은 상호 의존관계를 설정할 수 없어 초기화가 어려움

예를 들어 비밀번호 검증 기능이 플러그인형태였다가 8.0 에서는 컴포넌트로 개선되었다.

### 4.1.6 쿼리 실행 구조 ###

MySQL 엔진에서

- 쿼리 파서 -> 전처리기 -> 옵티마이저 -> 쿼리 실행기

이후 스토리지 엔진에서 처리한다.

### 4.1.6.1 쿼리 파서 ###

사용자 요청으로 들어온 쿼리 문장을 토큰으로 분리해 트리형태의 구조로 만들어 내는 작업을 말한다. 문법 오류는 이 과정에서 발견하고 오류 메시지를 전달한다.

### 4.1.6.2 전처리기 ###

파서 트리를 기반으로 쿼리 문장에 구조적인 문제점이 없는지 확인한다. 각 토큰을 테이블 이름이나 칼럼 이름, 또는 내장 함수와 같은 객체를 매핑해 해당 객체의 존재 여부와 객체의 접근 권한 등을 확인하는 과정을 이 단계에서 수행한다. 실제 존재하지 않거나 권한상 사용할 수 없는 토큰은 이 단계에서 걸러진다.

### 4.1.6.3 옵티마이저 ###

사용자의 요청으로 들어온 쿼리 문장을 저렴한 비용으로 가장 빠르게 처리하는 방법을 결정한다. 쿼리 변환, 비용 최적화, 실행 계획 수립이 일어난다.

### 4.1.6.4 실행 엔진 ###

옵티마이저가 두뇌라면 실행 엔진과 핸들러는 손과 발이다. 실행엔진은 만들어진 계획대로 각 핸들러에게 요청해서 받은 결과를 또 다른 핸들러 요청의 입력으로 연결하는 역할을 한다.

### 4.1.6.5 핸들러(스토리지 엔진) ###

핸들러는 결국 스토리지 엔진이며 MyISAM 테이블을 조작하면 핸들러가 MyISAM 스토리지 엔진이 되고 InnoDB 테이블을 조작하는 경우에는 핸들러는 InnoDB 스토리지 엔진이 된다.

### 4.1.7 복제 ###

별도의 장에서 다룹니다

### 4.1.8 쿼리 캐시 ###

쿼리 캐시는 SQL의 실행결과를 메모리에 캐시하고 동일한 SQL 쿼리가 실행되면 테이블을 읽지 않고 즉시 반환하여 성능이 매우 좋을 수 있다. 하지만 테이블이 변경되면 캐시에 저장된 데이터중 관련된 것들을 모두 삭제해야해서 오히려 성능이 떨어질 수 있다. MySQL 8.0 오면서 쿼리 캐시는 MySQL 서버의 기능에서 완전히 삭제되었다.

### 4.1.9 스레드 풀 ###

커뮤니티 에디션은 지원하지 않지만 엔터프라이즈 버전에서는 지원한다. 스레드 풀은 내부적으로 사용자의 요청을 처리하는 스레드 개수를 줄여서 동시 처리되는 요청이 많다고 하더라도 MySQL 서버의 CPU가 제한된 개수의 스레드 처리에만 집중할 수 있게 해서 서버의 자원 소모를 줄이는 것이 목적이다. 하지만 실제 서비스에서 눈에 띄는 성능 향상을 보여주는 경우는 드물다.

### 4.1.10 트랜잭션 지원 메타 데이터 ###

데이터 베이스 서버에서 테이블의 구조 정보와 스토어드 프로그램 등의 정보를 데이터 딕셔너리 혹은 메타 데이터라고 하며 5.7 까지는 테이블의 구조를 FRM 파일에 저장하고 일부 스토어드 프로그램 또 한 파일 기반으로 관리 했다. 하지만 파일 기반은 트랜잭션을 지원하지 않아 데이터 베이스가 깨지는 현상이 있었다. 8.0 부터는 테이블의 구조 정보나 스토어드 프로그램의 코드 관련 정보를 모두 InnoDB의 테이블에 저장한다. MySQL 서버가 작동하는데 필요한 테이블을 묶어서 시스템 테이블이라고 하는데 대표적으로 인증, 권한에 관한 테이블들이 있다. mysql DB에 저장되며 mysql.ibd 라는 테이블 스페이스에 저장된다.

## 4.2 InnoDB 스토리지 엔진 아키텍처 ##

InnoDB는 레코드 기반의 잠금을 제공하여 동시성 처리를 안정적으로하고 성능이 뛰어나다.

### 4.2.1 프라이머리 키에 의한 클러스터링 ###

InnoDB의 모든 테이블은 기본적으로 pk를 기준으로 클러스터링 되어 저장된다. 모든 세컨더리 인덱스는 pk 값을 논리적인 주소로 사용한다. 프라이머리키를 이용한 레인지 스캔이 빠르게 처리된다. MyISAM에서는 클러스터링 키를 지원하지 않는다. 그저 유니크 제약을 가진 세컨더리 인덱스일 뿐이다.

### 4.2.2 외래 키 지원 ###

외래 키에 대한 지원은 InnoDB 스토리지 엔진 레벨에서 지원하는 기능으로 MyISAM 이나 Memory 테이블에서는 사용할 수 없다. 외래키는 데이터 베이스 서버의 운영의 불편함으로 생성하지 않는 경우도 있는데 개발 환경에서의 데이터 베이스에서는 좋은 가이드 역할을 한다. InnoDB의 외래키는 부모테이블, 자식 테이블 모두 해당 칼럼에 대해 인덱스 생성이 필요하고 변경 시 데이터가 존재하는지 판단하는 작업이 필요해서 잠금이 필요하여 데드락이 발생하는 경우가 많아서 외래키의 존재는 주의하는 것이 좋다.

### 4.2.3 MVCC(Multi Version Concurrency Control) ###

MVCC 의 가장 큰 목적은 잠금없는 일관된 읽기 작업을 제공하는데 있다. 멀티 버전은 하나의 레코드에 대해 여러 개의 버전이 동시에 관리되는 것을 말한다. 시스템 격리 수준에 따라 select 의 결과가 달라질 순 있지만 언두로그를 활용해 실행된다. 언두 영역을 필요로 하는 트랜잭션이 없으면 삭제된다.

### 4.2.4 잠금 없는 일관된 읽기(Non-Locking Consistent Read) ###

InnoDB 는 MVCC 기술을 이용해 잠금을 걸지 않고 읽기 작업을 수행한다. 다른 트랜잭션이 가지고 있는 잠금을 기다리지 않고 읽기 작업이 가능하다. 격리 수준이 SEREALIZABLE 이 아닌 경우 INSERT 와 연결되지 않은 순수한 SELECT 작업은 다른 트랜잭션의 변경 작업과 관계없이 대기하지 않고 즉시 실행된다. InnoDB 에서는 변경되기 전의 데이터를 읽기 위해 언두 로그를 사용한다.

### 4.2.5 자동 데드락 감지 ###

InnoDB 는 내부적으로 잠금이 교착상태에 빠지지 않았는지 잠금 대기 몰고을 그래프 형태로 관리한다. InnoDB 스토리지 엔진은 데드락 감지 스레드를 가지고 있어서 데드락 감지 스레드가 주기적으로 잠금 대기 그래프를 검사해 교착 상태에 빠진 트랜잭션들을 찾아서 그 중 하나를 강제로 종료한다. 이 때 종료할 트랜잭션을 결정할 때는 언두 로그양이다. 트랜잭션 강제 롤백으로 인한 MySQL 서버의 부하를 줄일 수 있기 때문이다.단 MySQL 엔진에서 관리하는 테이블 잠금 명령은 볼 수가 없어서 데드락 감지가 불확실할 수도 있다. innodb_table_locks시스템 변수를 활성화하면 된다. 데드락 관리를 innodb_deadlock_detect 시스템 변수로 제공하여 off 로 끌 수도 있다. off 를 한 경우 innodb_lock_wait_timeout 시스템 변수를 사용하여 잠금을 설정한 시간동안 획득하지 못하면 쿼리를 실패하게 만들 수 있다.

### 4.2.6 자동화된 장애 복구 ###

손실이나 장애로부터 데이터를 보호해야 한다. InnoDB 데이터 파일은 기본적으로 MySQL 서버가 시작될 때 항상 자동 복구를 수행한다. 자동으로 복구할 수 없다면 자동 복구를 멈추고 서버는 종료한다.
MySQL 서버가 시작되고 InnoDB 테이블이 인식된다면 mysqldump를 이용해 데이터를 가능한 백업한다.

innoDB의 복구에는 innodb_force_recovery 변수로 1~6까지의 값이 있다.

- 1 SRV_FORCE_IGNORE_CORRUPT
- 2 SRV_FORCE_NO_BACKGROUND
- 3 SRV_FORCE_NO_TRX_UNDO
- 4 SRV_FORCE_NO_IBUF_MERGE
- 5 SRV_FORCE_NO_UNDO_LOG_SCAN
- 6 SRV_FORCE_NO_LOG_READ
  
6단계외에도 MySQL 서버가 시작되지 않는다면 백업을 이용해 다시 구축하는 수밖에 없다.
