# 제1절 인덱스 기본 원리

## 1. 인덱스 기본 구조
`B\*Tree 인덱스 구조가 기본`이다(B는 Balance를 뜻한다)  
B\*Tree는 이중탐색트리에서 파생된 자료구조라고 할 수 있다.(파생으로 B\*tree, B\+tree 등등이 존재한다.)

> Oracle : 인덱스 구성 칼럼이 모두 null인 레코드는 인덱스 저장 안 함  
> SQL Server : 모두 null인 레코드도 인덱스에 저장한다.

## 2. 인덱스 스캔 방식
1. `Index Range Scan`  
![index range scan](./img/index_range_scan.png)  
수직 탐색 후 블록을 필요한 범위만 스캔하는 방식  
Index Range Scan결과 집합은 인덱스 칼럼 순으로 정렬된다.
2. `Index Full Scan`  
![index full scan](./img/index_full_scan.png)  
인덱스 리프 블록을 처음부터 끝
까지 수평적으로 탐색(수직 탐색은 LMC를 찾는 처음 한번 발생)
3. `Index Unique Scan`  
![index unique scan](./img/index_unique_scan.png)  
수직적 탐색만으로 데이터 스캔
4. `Index Skip Scan`  
![index skip scan](./img/index_skip_scan.png)  
조건에 부합하는 레코드를 포함할 가능성이 있는 하위블록만 골라서 액세스  
인덱스 선두 칼럼의 Distint Value 개수가 적고 후행 칼럼의 Distint Value 개수가 많을 때 유용하다.
5. `Index Fast Full Scan`  
인덱스 트리 구조를 무시하고 인덱스 세그먼트 전체를 Multiblock Read 방식으로 스캔
6. `Index Range Scan Descending`  
![index range scan descending](./img/index_range_scan_descending.png)  
Index Range Scan과 동일한 스캔방식이지만 내림차순으로 정렬된 결과 집합을 얻는다는 점이 다르다.

## 3. 인덱스 종류
1. `B*Tree 인덱스`  
Balanced Index. 기본 지원 인덱스
2. `비트맵 인덱스`  
Distinct Value 개수가 적을 때 비트맵 인덱스를 사용하면 저장효율이 매우 좋다.
3. `함수기반 인덱스`  
칼럼에 특정 함수를 적용한 값으로 B*Tree 인덱스를 만든다.  
데이터 입력, 수정 시 함수를 적용해야 하므로 다소 부하가 발생할 수 있다.
4. `리버스 키 인덱스`  
입력된 키 값을 거꾸로 변환해서 저장  
부등호나 between, like 같은 범위 검색 조건에는 사용할 수 없다.
5. `클러스터 인덱스`  
클러스터 키 값이 같은 레코드가 한 블록에 모이도록 저장하는 구조  
해당 키 값을 저장하는 첫번째 데이터 블록만 가리킨다.
6. `클러스터형 인덱스와 IOT(Index-Organized Table) 구조`  
모든 행 데이터를 인덱스 리프페이지에 저장  
클러스터형 인덱스는 체이블마다 단 하나만 생성 가능  
넓은 범위의 데이터를 검색할 때 유리  

# 제2절 테이블 액세스 최소화  
인덱스 튜닝 = `테이블 랜덤 액세스 최소화` + `인덱스 스캔범위 최소화`  
1. 인덱스를 경유한 테이블 랜덤 액세스가 성능 저하의 주요인이다.  
2. 인덱스 칼럼 기준으로 테이블 데이터가 모여있는 정도(`클러스터링팩터`)에 따라 인덱스 손익분기점이 많이 달라진다.  
3. 테이블 액세스 최소화를 위한 일반적인 방법은 인덱스에 칼럼을 추가하는 것이다.  
4. IOT, 클러스터형 인덱스, 클러스터 테이블 등을 적용해서 테이블 액세스를 최소화할 수 있다.  

# 제3절 인덱스 스캔 최소화
1. 인덱스 선두 칼럼을 '='이나 In-List 조건으로 바꿔주는 것이 효과적
2. `Index Skip Scan`을 사용하는 것도 효과적이다.


# 제4절 인덱스 설계
인덱스 스캔 방식에 가장 일반적인 방식은 `Index Range Scan`  
1. 결합 인덱스 구성시 자주 사용되는 칼럼들을 선정하는 것
2. 선정된 칼럼 중 '=' 조건으로 자주 조회되는 칼럼을 앞쪽에 두는 것


> 카디널리티`Cardinality`와 선택도`Selectivity`  
> 카디널리티는 중복수치를 나타내는 지표이다.  
> value의 중복도가 낮으면 카디널리티가 높다. 반대로 중복 정도가 높으면 카디널리티가 낮다.  
> 선택도는 특정 값이 전체 행 중에 어느정도 비율을 차지하는가이고  
> 조회되는 비율이 적을수록 선택도가 낮다. 조회되는 비율이 높으면 선택도가 높다.  
> `카티널리티가 높으면 인덱스로 적합. 선택도가 낮으면 인덱스로 적합`  


> 이미지 출처  
> http://wiki.gurubee.net/pages/viewpage.action?pageId=28116440