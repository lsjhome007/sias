# 1. 아파치 스파크 소개

## 1.1 스파크란

아파치 스파크는 하둡의 맵 리듀스를 빠르게 대체하는 흥미롭고 새로운 빅데이터 처리 플랫폼이다.

아파치 하둡은 분산 컴퓨팅용 자바 기반 오픈소스 프레임워크로 하둡의 분산 파일 시스템 (Hadoop Distributed File system, HDFS)과 맵리듀스 처리 엔진으로 구성된다.

스파크는 범용 분산 컴퓨팅 플랫폼이라는 점에서 하둡과 유사하지만, 대량의 데이터를 메모리에 유지한 독창적인 설계로 계산 속도를 대폭 끌어올렸다. 맵리듀스보다 약 100배 빠르다.

스파크에서는 분산 아키텍쳐 때문에 처리 시간에 약간의 오버헤드가 필연적으로 발생한다. 따라서 대량의 데이터를 다룰 때는 오버헤드가 무시할 수 있는 수준이지만, 단일 머신에서도 충분히 처리할 수 있는 데이터셋을 다룰 때는 작은 데이터셋의 연산에 최적화된 다른 프레임워크를 사용하는 편이 낫다.

스파크는 온라인 트랜잭션 처리(Online Transaction Processing, OLTP) 애플리케이션을 염두해 두고 설계되지 않았다. 즉 대량의 원자성(atomicity) 트랜젝션을 빠르게 처리하는 작업에는 적합하지 않다. 반면 일괄 처리 작업이나 데이터 마이닝 같은 온라인 분석 처리(Online Analytical Processing, OLAP) 작업에는 적합하다.

### 1.1.1 스파크가 가져온 혁명

HDFS와 맵리듀스 처리 엔진으로 구성된 하둡 프레임워크는 데이터 분산 처리에서 고민해야 하는 다음 세 가지 주요 문제를 해결했다.

- 병렬 처리(parallelization): 전체 연산을 잘게 나누어 동시에 처리하는 방법
- 데이터 분산(distribution): 데이터를 여러 노드로 분산하는 방법
- 장애 내성(fault tolerance): 분산 컴포넌트의 장애에 대응하는 방법

### 1.1.2 맵리듀스의 한계

맵리듀스 잡의 결과를 다른 잡에서 사용하려면 먼저 이 결과를 HDFS에 저장해야 한다. 따라서 맵리듀스는 이전 잡의 결과가 다음 잡의 입력이 되는 반복 알고리즘에는 본질적으로 맞지 않다.

### 1.1.3 스파크가 가져다준 선물

스파크의 핵심은 맵리듀스처럼 잡에 필요한 데이터를 매번 가져오는 대신 데이터를 메모리에 캐시로 저장하는 인-메모리 실행 모델에 있다. 때문에 동일한 작업을 맵리듀스에 비해 100배 더 빠르게 실행할 수 있다. 이는 특히 머신 러닝, 그래프 알고리즘 등 반복 알고리즘과 기타 데이터를 재사용하는 모든 유형의 작업에 많은 영향을 주었다.

#### 1.1.3.1 사용 편의성

스파크는 스칼라, 자바 ,파이썬 R을 아우른느 다양한 프로그래밍 언어를 지원해 더 많은 사용자를 포옹할 수 있다. 또한 스파크가 제공하는 대화형 콘솔인 스파크 셸을 이용해 간단한 실험을 하거나 아이디어를 테스트할 수 있다. 스파크 셸을 사용하면 프로그램 문제를 진단하려고 컴파일과 배포를 끊임없이 반복하지 않아도 되며, 전체 데이터를 처리하는 작업도 REPL(Read-Eval-Print Loop)에서 실행할 수 있다.

#### 1.1.3.2 통합 플랫폼

스파크는 하둡 생태계의 여러 도구가 제공한 다양한 기능을 하나의 플랫폼으로 통합했다. 실시간 스트림 데이터 처리, 머신러닝, SQL연산, 그래프 알고리즘, 일괄 배치처리 등 여러 종류의 프로그램을 단일 프레임워크에서 구현할 수 있다.

#### 1.1.3.3 안티 패턴

스파크는 일괄 분석을 염두해 두고 설계되었기 때문에 공유된 데이터를 비동기적으로 갱신하는 연산(예: 온라인 트랜잭션 처리)에는 적합하지 않다. (실시간 데이터를 처리하는 스파크 스트리밍은 단순히 타임 윈도로 분할한 스트림 데이터에 일괄 데이터를 적용한 것이다.)

또한 스파크는 잡(job)과 테스크를 시작하는데 상당한 시간을 소모하기 때문에 대량의 데이터를 처리하는 작업이 아니라면 굳이 스파크를 사용할 필요가 없다. 소량의 데이털르 처리할 때는 스파크 같은 분산 시스템보다 간단한 관계형 데이터베이스나 잘 짜인 스크립트가 훨씬 더 빠르다.

## 1.2 스파크를 구성하는 컴포넌트

스파크는 여러 특수한 목적에 맞게 설계된 다양한 **컴포넌트**로 구성된다.

- 스파크 코어(core)
- 스파크 SQL
- 스파크 스트리밍
- 스파크 GraphX
- 스파크 MLlib

### 1.2.1 스파크 코어

스파크 코어는 스파크 잡과 다른 스파크 컴포넌트에 필요한 기본 기능을 제공한다. 스파크 코어에서 가장 중요한 개념은 스파크 API의 핵심 요소인 RDD(Resilient Distributed Dataset)이다.

RDD는 분산 데이터 컬렉션(데이터셋)을 추상화한 객체로 대이터셋에 적용할 수 있는 연산 및 변환 메서드를 함께 제공한다. RDD는 노드에 장애가 발생해도 데이터셋을 재구성할 수 있는 복원성을 갖추었다.

스파크 코어는 HDFS, GlusterFS, 아마존 S3 등 다양한 파일 시스템에 접근할 수 있다. 또 공유 변수(broadcast variable)와 누적 변수(accumulator)를 사용해 컴퓨팅 노드 간에 정보를 공유할 수 있다.

### 1.2.2 스파크 SQL

스파크 SQL은 스파크와 하이브 SQL이 지원하는 SQL을 사용해 대규모 분산 정형데이터를 다룰 수 있는 기능을 제공한다. 스파크 버전 1.3에 도입된 DataFrame과 스파크 버전 1.6에 도입된 Dataset은 정형 데이터의 처리를 단순화하고 성능을 크게 개선했다. JSON 파일, Parquet 파일(데이터와 스키마 모두 저장되는 파일 포멧), 관계형 DB 테이블, 하이브 테이블 등 다양한 정형 데이터를 읽고 쓰는데도 활용된다.

스파크 SQL은 DataFrame과 Dataset에 적용될 연산을 일정 시점에 RDD 연산으로 변환해 일반 스파크 잡으로 실행한다. 스파크 SQL은 카탈리스트(Catalyst)라는 쿼리 최적화 프레임워크를 제공하며, 사용자가 직접 정의한 최적화 규칙을 적용해 프레임워크를 확장할 수도 있다.

### 1.2.3 스파크 스트리밍

스파크 스트리밍은 다양한 데이터 소스에서 유입되는 실시간 스트리밍 데이터를 처리하는 프레임워크다. 스파크가 지원하는 스트리밍 소스에는 HDFS, 아파치 카프카, 아파치 플롬(Flume), 트위터, ZeroMQ가 있다. 

스파크 스트리밍은 장애가 발생시 연산 결과를 자동으로 복구한다. 스파크 스트리밍은 **이산 스트림**(Discretized Stream, DStream)방식으로 스트리밍 데이터를 표현하는데, 가장 마지막 타임 윈도 안에 유입된 데이터를 RDD로 구성해 주기적으로 생성한다.

스파크 스트리밍과 다른 스파크 컴포넌트를 단일 프로그램에서 사용해서 실시간 처리 연산과 머신러닝 작업, SQL 연산, 그래프 연산 등을 통합할 수도 있다.

### 1.2.4 스파크 MLlib

로지스틱 회귀, 나이브 베이즈 분류, 서포트 벡터 머신, 의사 결정 트리, 랜덤 포레스트, 선형 회귀, k-평균 군집화 등 다양한 머신러닝 알고리즘을 지원한다.

### 1.2.5 스파크 GraphX

그래프는 정점과 두 정점을 잇는 간선으로 구성된 데이터 구조이다. 스파크 GraphX는 그래프 RDD형태의 그래프 구조를 만들 수 있는 다양한 기능을 제공한다.

GaphX에는 페이지랭크(page rank), 연결요소(connects components), 최단 경로(shortest path) 탐색, SVD++(Singular Value Decomposition++)등 그래프 이론에서 가장 중요한 알고리즘이 구현되어 있다.
