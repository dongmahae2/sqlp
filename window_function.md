# SQL window function

## RANK
## ROW_NUMBER
## SUM
## MAX, MIN
## AVG
## COUNT

---
## DENSE_RANK
```SQL
--DENSE_RANK 동일 순위를 하나의 건수로 취급
SELECT job, ename, sal
     , DENSE_RANK() OVER(ORDER BY sal DESC) AS dr
  FROM tb_sell_product
```
## ROWS BETWEEN 
```SQL
-- PRECEDING FOLLOWING 파티션 내에서 앞 뒤로 한건씩 비교
SELECT mgr
     , ename
     , hiredate
     , sal
     , ROUND(AVG(sal) OVER(PARTITION BY hiredate
                           ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING)) AS avg_sal
  FROM emp
```

## FISRT_VALUE, LAST_VALUE
### min, max를 이용해서 구할 수도 있다.
```SQL
-- 현재 행을 기준으로 파티션 내 첫 번째 행까지의 범위
SELECT deptno
     , ename
     , sal
     , FIRST_VALUE(ename) OVER(PARTITION BY deptno ORDER BY sal DESC, ename
                               ROWS UNBOUNDED PRECEDING) AS ename_fv
  FROM emp

--  현재 행을 포함해서 파티션 내의 마지막 행까지의 범위
SELECT deptno
     , ename
     , sal
     , LAST_VALUE(ename) OVER(PARTITION BY deptno ORDER BY sal DESC, ename
                               ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) AS ename_fv
  FROM emp
```
## LAG, LEAD
### 파티션별 윈도우에서 이전, 이후 몇 번째 행의 값을 가져올 수 있다.
```SQL
-- 두번째 인자는 디폴트 값이 1이고 몇번째 앞의 인자를 가져올지 결정, 세번째는 null 일경우 초기값 설정(생략가능)
SELECT ename
     , hiredate
     , sal
     , LAG(sal, 1, 0) OVER(ORDER BY hiredate) AS lag_sal
  FROM emp
 WHERE job = 'SALESMAN'
 
-- 직원들을 입사일 기준 정렬하고, 다음에 입사한 인력의 입사일자를 함께 출력
SELECT ename
     , hiredate
     , sal
     , LEAD(hiredate , 1) OVER(ORDER BY hiredate) AS lead_hiredate
  FROM emp
 WHERE job = 'SALESMAN'
```
## RATIO_TO_REPORT
### 파티션 전체 합계에 대한 칼럼 값의 백분율
```SQL
SELECT ename
     , sal
     , ROUND(RATIO_TO_REPORT(sal) OVER(), 2) AS sal_pr
  FROM enp
 WHERE job = 'SALESMAN'
```

## PERCENT_RANK
### 제일 먼저 나오는 값을 0, 제일 늦게 나오는 값을 1로 순서별 백분율
```SQL
SELECT deptno
     , ename
     , sal
     , PERCENT_RANK() OVER(PARTITION BY deptno ORDER BY sal DESC) AS pr
  FROM emp
```


## CUM_DIST
### 전체 건수에서 현재 행보다 작거나 같은 건수에 대한 누적백분율
```SQL
SELECT deptno
     , ename
     , sal
     , CUME_DIST() OVER(PARTITION BY deptno ORDER BY sal DESC) AS cd
  FROM emp
```

## NTILE
### 전체 건수를 ARGUMENT 값으로 N 등분한 결과
```SQL
SELECT ename
     , sal
     , NTILE(4) OVER(ORDER BY sal DESC) AS nt
  FROM emp
```