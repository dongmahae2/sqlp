# Regular Expression

|연산자|영문|설명|
:---:|:---:|---|
.|dot|모든문자와 일치
\||or|대체문자를 구분
\\ |backslash|다음 문자를 일반 문자로 취급
^|carrot|문자열의 시작
$|dollar|문자열의 끝


|연산자|설명|
:---:|---|
?|0회 또는 1회 일치
*|0회 또는 그 이상의 횟수로 일치
+|1회 또는 그 이상의 횟수로 일치
{m}| m회 일치
{m,}| 최소 m회 일치
{,m}| 최대 m회 일치
{m,n}| 최소 m회, 최대 n회 일치
||
(expr)|괄호 안의 표현식을 하나의 단위로 취급
\\n|n번째 서브 표현식과 일치, n은 1에서 9 사이의 정수
[char...]|문자 리스트 중 한 문자와 일치
[^char...]|문자 리스트에 포함되지 않은 한 문자와 일치

|연산자|설명|동일|
:---:|:---:|---|
[:digit:]|숫자|[0-9]
[:lower:]|소문자|[a-z]
[:upper:]|대문자|[A-Z]
[:alpha:]|영문자|[a-zA-Z]
[:alnum:]|영문자와 숫자|[0-9a-zA-Z]
[:xdigit:]|16진수|[0-9a-fA-F]
[:punct:]|구두점 기호|[^[:alnum:][:cntrl:]]
[:blank:]|공백 문자|
[:space:]|공간 문자 (space, enter, tab)|
 | | 
\d|숫자|[[:digit:]]
\D|숫자가 아닌 모든 문자|[^[:digit]]
\w|숫자와 영문자(underbar 포함)|[[:alnum:]_]
\W|숫자와 영문자가 아닌 모든 문자(underbar 제외)|[^[:alnum:]_]
\s|공백문자|[[:space:]]
\S|공백문자가 아닌 모든 문자|[^[:space:]]


|연산자|설명|
:---:|:---:|
??|0회 또는 1회 일치
*?|0회 또는 그 이상 횟수로 일치
+?|1회 또는 그 이상의 횟수로 일치
{m}?|m회 일치
{m,}?|최소 m회 일치
{,m}?|최대 m회 일치
{m, n}?|최소 m회, 최대 n회 일치

> 패턴을 최소로 일치 시키는 비탐욕적 방식(nonegreedy)으로 동작
```SQL
SELECT REGEXP_SUBSTR('aaaa', 'a{2,}?') -- aa    nonegreedy 방식
     , REGEXP_SUBSTR('aaaa', 'a{2,}')  -- aaaa  greedy 방식
  FROM DUAL;
```


# 정규 표현식 조건과 함수
## REGEXP_LIKE
```SQL
-- 패턴과 일치하면 true 아니면 false 
SELECT first_name
     , last_name
  FROM HR.employees
 WHERE REGEXP_LIKE(first_name, '^Ste(v|ph)en$');
```

## REGEXP_REPLACE
```SQL
-- 일치한 패턴을 지정한 문자열로 변경하여 반환
SELECT phone_number                                                             -- 650.121.2004
     , REGEXP_REPLACE(phone_number,
                    , '([[:digit:]]{3})\.([[:digit:]]{3})\.([[:digit:]]{4}))'
                    , '(\1) \2-\3') AS phone_number2                            -- (650) 121-2004
  FROM HR.employees
 WHERE REGEXP_LIKE(first_name, '^Ste(v|ph)en$');
```

## REGEXP_SUBSTR
```SQL
-- 일치한 패턴을 반환
SELECT REGEXP_SUBSTR('http://www.example.com/products')
     , 'http://([[:alnum:]]+\.?){3,4}/?') AS url            -- http://www.example.com/
  FROM DUAL
```

## REGEXP_INSTR
```SQL
-- 일치한 패턴의 시작 위치를 정수로 반환한다.
SELECT REGEXP_INSTR('1234567890', '(123)(4(56)(78))', 1, 1, 0, 'i', 1) AS C1    -- 1
     , REGEXP_INSTR('1234567890', '(123)(4(56)(78))', 1, 1, 0, 'i', 2) AS C2    -- 4
     , REGEXP_INSTR('1234567890', '(123)(4(56)(78))', 1, 1, 0, 'i', 4) AS C3    -- 7
  FROM DUAL
```

## REGEXP_COUNT
```SQL
-- 일치한 패턴의 횟수를 반환한다.
SELECT REGEXP_INSTR('123123123123123', '123', 1) AS C1  -- 5
     , REGEXP_INSTR('123123123123', '123', 3)    AS C2  -- 3
  FROM DUAL
```
