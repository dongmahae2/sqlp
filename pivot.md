# PIVOT
pivot > 행을 열로  
unpivot > 열을 행으로
```SQL
-- PIVOT 절은 집계함수와 FOR 절에 지정되지 않은 열을 기준으로 집계되므로 인라인 뷰를 통해 열을 지정해줘야함
-- FOR 절은 PIVOT할 열을 지정한다.
-- IN 절은 PIVOT할 열 값을 지정한다.
SELECT *
  FROM (SELECT job, deptno, sal FROM emp)
 PIVOT (SUM(sal) FOR deptno IN (10, 20, 30))
 ORDER BY 1

-- PIVOT 절 대신 집계함수와 CASE를 사용해도 같은 결과를 얻을 수 있다.
SELECT job
     , SUM(CASE deptno WHEN 10 THEN sal END) AS d10_sal
     , SUM(CASE deptno WHEN 20 THEN sal END) AS d20_sal
     , SUM(CASE deptno WHEN 30 THEN sal END) AS d30_sal
  FROM emp
 GROUP BY job
 ORDER BY job
```

```SQL
SELECT *
  FROM (SELECT TO_CHAR(hiredate, 'YYYY') AS yyyy, job, deptno, sal FROM emp)
 PIVOT (SUM(sal) FOR deptno IN (10, 20, 30))
 ORDER BY 1
```

```SQL
-- 집계함수와 IN 절에 별칭을 지정했다.
SELECT *
  FROM (SELECT job, deptno, sal FROM emp)
 PIVOT (SUM(sal) AS sal FOR deptno IN (10 AS d10, 20 AS  D20, 30 AS D30))
 ORDER BY 1
```

# UNPIVOIT
열이 행으로 전환
```SQL
-- UNPIVOT을 위해 다음과 같은 테이블 생성
CREATE TABLE t1 AS
SELECT job, d10_sal, d20_sal, d10_cnt, d20_cnt
  FROM (SELECT job, deptno, sal FROM emp WHERE job IN ('ANALYST', 'CLERK'))
 PIVOT (SUM(sal) AS sal, count(*) FOR deptno IN (10 AS d10, 20 AS d20));

SELECT job, deptno, sal
  FROM t1
UNPIVOT (sal FOR deptno IN (d10_sal, d20_sal)
 ORDER BY 1, 2;
```


```SQL
-- INCLUDE NULLS : 열의 값이 NULL인 행도 결과에 포함.
SELECT job, deptno, sal
  FROM t1
UNPIVOT INCLUDE NULLS (sal FOR deptno IN (d10_sal, d20_sal)
 ORDER BY 1, 2;
```

```SQL
-- 카티션 곱, CASE 표현식을 사용하면 UNPIVOT과 동일한 결과를 얻을 수 있다.
SELECT A.job
     , CASE B.lv WHEN 1 THEN 10        WHEN 2 THEN 20        END AS deptno
     , CASE B.lv WHEN 1 THEN A.d10_sal WHEN 2 THEN A.d20_sal END AS deptno
     , CASE B.lv WHEN 1 THEN A.d10_cnt WHEN 2 THEN A.d20_cnt END AS deptno
  FROM t1 A
     , (SELECT LEVEL as LV FROM dual CONNECT BY LEVEL <=2) B
 ORDER BY 1, 2
```
