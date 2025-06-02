# 학사관리 과제 활용 SQL 명령어

## UNION
- 여러 쿼리문 합쳐서 하나의 쿼리문으로 만듦
- 중복된 값을 제거한다.
- JOIN과 차이점 (참조키를 활용하여 두 테이블 연결)
  - join : 새로운 열로 결합 (수평결합), 연결하면 새로운 열이 추가된다.
  - union : 새로운 행으로 결합 (수직결합), 연결하면 새로운 행이 추가된다. 동일한 학번 또 나오게 된다. 학점 필드에 평균 점수가 나오기도 한다.
- 조건
  - 필드명 동일
  - 데이터 타입 동일
  - 필드 개수 동일

<질의28> 점수가 80점 이상이거나 수강한 과목이 2개 이상인 학생의 학번을 구하라.
```sql
select stu_no
from enrol
where enr_grade >= 80
UNION
select stu_no
from enrol
group by stu_no
having count(*) >= 2;
```
- UNION ALL : 두질의 결과값의 합으로 중복을 포함한다.
- INTERSECT : 결과값의 공통되는 값
- MINUS : 첫 번째 질의 결과에서 두 번째 질의 결과에 있는 행을 제거한 값

## 수정문 UPDATE
- 단일 튜플 변경

<질의29> 학번이 20182032인 학생의 이름이 '황지후＇로 변경되었다

```sql
update student -- student 테이블 수정
set stu_name = '황지후' -- name을 황지후로 바꾼다.
where stu_no = 20182021; -- 조건 : no가 20182021인 행의
```
- 다중 튜플 변경

<질의30> 과목번호 101의 점수가 10점 올랐다.

```sql
update enrol
set enr_grade = enr_grade + 10 -- enr_grade 필드에 기존 값 + 10
where sub_no = 101; 조건 : sub_no가 101인 행의
```

## 삽입문 INSERT
- 다중 튜플 삽입

<질의31> 컴퓨터정보과 1학년 B반에 20191032 김미영 여학생이 입학하였다. 이를 데이터베이스에 반영하시오.

```sql
insert
into student -- 빈 값을 NULL로 지정하지 않겠다면 모든 필드명을 student()안에 넣어야 한다.
values(20191032, '김미영', NULL, '컴퓨터정보', 1, 'B', 'F', NULL, NULL);
```
## 삭제문 DELETE
-단일 튜플 삭제

<질의32> 옥성우 학생이 자퇴하였다. 이를 데이터베이스에 반영하시오.

```sql
delete
from student
where stu_name = '옥성우';
```

- 다중 튜플 삭제

<질의33> 수강 테이블에서 김종헌 학생의 모든 자료를 삭제하라.

```sql
delete
from enrol
where stu_no = ( -- enrol에는 학번만 나와있으니 김종헌이 누구인지 특정하지 못한다. 그래서 서브쿼리 사용.
select stu_no
from student
where stu_name = '김종헌');
```

<질의34> 수강 테이블의 모든 자료를 삭제하라.
```sql
delete
from enrol;
```

# 9주차 인사관리 활용 SQL 명령어

## 중복 제거 DISTINCT
<질의4> 사원들의 입사일자를 중복을 제거하고 검색하라.
```sql
SELECT DISTINCT hiredate
from emp;
```

## ~의 합 sum()
<질의8> 사원들의 6개월 급여의 합을 구하라.
```sql
SELECT sum(sal * 6 )
from emp;
```

## 조건 WHERE
<질의10> 10번 부서에 근무하는 사원들의 이름을 검색하라.
```sql
SELECT ename
from emp
WHERE deptno = 10;
```
<질의11> 급여가 2000이상인 사원들의 사원번호, 이름을 검색하라. (정수 비교)
```sql
select empno, ename
from emp
where sal >= 2000;
```
<질의13> 직무가 “CLERK”인 사원들의 사원번호, 이름을 검색하라. (문자열 활용)
```sql 
select empno, ename
from emp
where job = "CLERK";
```

## 평균 avg() 날짜/시간 #1900-10-10#
<질의12> 81년 이후에 입사한 사원들의 평균 급여를 구하라.
```sql
select avg(sal)
from emp
where hiredate > #1981-12-31#;
```
## 날짜데이터 
- 년 YEAR()
- 월 MONTH()
- 일 DAY()
- 시 HOUR()
- 분 MINUTE()
- 초 SECOND
### Format()
- 날짜 표현시 반드시 쌍따옴표 ``""`` 사용
- format(hiredate, "yyyy")
- "mm","dd","hh","nn","ss"

<질의95> SCOTT 사원과 **같은 해**에 입사한 사원 정보를 검색하라.
```sql
select *
from emp
where ename <> 'scott'
and YEAR(hiredate) = ( -- or format(hiredate,"yyyy") 사용
select YEAR(hiredate)
from emp
where ename = 'scott'
);
```

## ~순으로 출력순서 정렬 ORDER BY -- 월 비교 month() -- 여러 조건 AND() -- 참조키 deptno
<질의18> 2월에 입사한 사원들의 모든 정보를 **부서명순**으로 구하라.
```sql
select *
from emp, dept
where emp.deptno = dept.deptno -- 참조키 활용해서 두 테이블 연결
AND month(hiredate) = 2 -- 월 비교 month()
order by dept.dname; -- 부서명순
```
<질의42> ACCOUNTING 부서 사원들의 사번과 이름을 **사원번호순**으로 검색하라.
```sql
select empno, ename
from emp, dept
where emp.deptno = dept.deptno
and dname = 'accounting'
order by 1; -- empno도 가능
```
<질의45> 사원들의 사원번호, 이름을 **부서번호별** 이름순으로 검색하라.
- ``order by``로 순서를 정할 때는 무슨 기준이 먼저 나오냐를 잘 봐야 한다.
- **부서번호별** 이름순이니까 무조건 부서번호부터 작성.
- 부서별, 직무별, 급여순 -> ``emp.deptno, emp,job, emp.sal desc``
```sql
select empno, ename
from emp
order by deptno, ename; -- 부서번호별! 이름순
```

## OR 하나 이상의 조건 만족
<질의81> 부서명이 ACCOUNTING**이거나,** 지역이 CHICAGO인 사원의 사원번호와 이름을 검색하라.
```sql
select empno, ename
from emp, dept
where emp.deptno = dept.deptno 
and dname = 'accounting'
or loc = 'chicago'; 
```

## 오름차순 asc (ascending), 내림차순 desc (descending) 
- 오름차순, 내림차순 구분은 어느 방향으로 작아지는지 생각하면 쉽다.
- 올라갈수록 작아지면 오름차순, 내려갈수록 작아지면 내림차순
- 작은 순으로 -> asc, 큰 순으로 -> desc
<질의46> 사원들의 정보를 **부서별** 급여가 많은 순으로 검색하라. 
```sql
select emp.*, dept.dname -- 모든사원정보, 부서별(이름)
from dept, emp
where dept.deptno = emp.deptno
order by dept.deptno, emp.sal desc; -- default값은 asc, emp.deptno도 가능
```

## 이외의, 같지 않다 <>
<질의19> 부서번호 30이외의 부서명과 지역을 검색하라.
```sql
select dname, loc
from dept
where deptno <> 30;
```

## 서브쿼리 (Subquery) or 중첩 질의 (Nested Query)
- 서브쿼리 : 쿼리 안에 쿼리 사용 
<질의21> ALLEN의 상사사원의 모든 정보를 검색하라.
```sql
select *
from dept
where empno = (select mgr -- 쿼리 안에 쿼리 사용, 알렌의 상사사원번호 추출 -> 상사사원번호는 사원번호니까 일치한 필드 찾음
from emp
where ename = "ALLEN");
```
<질의50> 10, 20번부서 사원들 중 최고(max) 급여를 받는(부질의) 사원의 사원번호, 이름, 급여를 구하라. 
- max() : 해당 필드에서 가장 큰 값 추출, 집계 함수라 empno, ename 등, 개별 행의 값과 동시 사용 불가능. 그래서 서브쿼리 사용. sum도 마찬가지.
- min() : 해당 필드에서 가장 작은 값 추출.
- 개별 행과 집계 함수는 동시에 사용이 불가능하지만, 집계 함수끼리는 가능하다. ``max(),min()``
```sql
select empno, ename, sal
from emp
where deptno in (10, 20) and sal = ( -- 최고급여(3000)이면서 10, 20 부서인 사원
select max(sal)
from  emp
where deptno in (10,20)
);
```
<질의74> 30번 부서에서 입사기간이 가장 오래된 사원의 이름을 검색하라.
- min(hiredate)로 입사기간을 찾아야 하는데 ename과 함께 사용 못하고 group by도 쓸 수 없다. -> 부질의 사용
```sql
select ename
from emp
where deptno = 30 -- 부서번호 == 정수 
and hiredate = ( -- 부질의에서 나온 입사기간과 같은 입사기간 찾기.
select min(hiredate)
from emp
where emp.deptno = 30
);
```
<질의88> BLAKE 사원보다 많은 급여를 받는 사원의 이름을 검색하라.
```sql
select ename
from emp
where sal > (
select sal
from emp
ename = 'blake'
);
```

## ~의 사이 between 1000 and 2500
<질의29> 급여가 1000이상이며, 2500이하인 사원의 사원번호와 이름을 검색하라. 
```sql
select empno, ename
from emp
where sal between 1000 and 2500; -- 1000과 2500 사이
```
<질의34> 1981년 2월에 입사한 사원의 사원번호, 이름, 부서번호를 검색하라. (날짜/시간 활용)
```sql
select empno, ename, deptno
from emp
where hiredate between #1981-02-01# and #1981-02-28#;
```

## 문자열 부분일치 LIKE ""
<질의30> 사원번호 75XX인 사원의 사원번호와 이름, 부서번호를 검색하라.
```sql
select empno, ename, deptno
from emp
where empno like '75*';
```
<질의35> 이름에 ‘A’가 들어있는 사원의 사원번호, 이름을 검색하라. '*A*'
```sql
select empno, ename
from emp
where ename like "*A*";
```
<질의43> 사원번호가 4로 끝나는 사원들의 사번과 이름을 사원번호순으로 검색하라. ('*4')
```sql
select empno, ename
from emp
where empno like '*4'
order by empno;
```


## 조회할 데이터 값이 여러 개 in (10,30)
<질의31> 부서번호 10 또는 30에 근무하는 사원들의 이름과 급여를 검색하라.
```sql
select ename, sal
from emp
where empno in (10,30);
```
<질의41> 직무가 ‘MANAGER’ 또는 ‘SALESMAN’인 사원의 사원번호, 이름, 부서번호를 검색하라. (문자열 활용)
```sql
select empno, ename, deptno
from emp
where job in ('manager', 'salesman');
```
<질의96> 20번 부서 사원의 직무와 같은 직무를 하고 있는 다른 부서의 사원 정보를 검색하라.
- ``in`` 대신 ``=``을 썼다면 서브쿼리의 결과가 여러 개라 동일한 값을 찾는데 오류가 발생한다.
- 그래서 in을 사용하여 여러 개의 결과를 가져온다. 다른 부서의 사원 정보이니, 한 명이 아닐 수 있기 때문.
- 부가적으로 DISTINCT를 활용하여 중복 제거하여 한 번만 처리하게 해주었다.
```sql
SELECT *
FROM emp
WHERE job IN (
    SELECT DISTINCT job
    FROM emp
    WHERE deptno = 20
)
AND deptno <> 20;
```

## NULL인 필드 구하기 is NULL
<질의36> 해당관리자가 NULL인 사원의 사원번호, 이름을 검색하라.
```sql
select empno, enamew
from emp
where mgr is NULL
```
<질의37> 해당관리자가 NULL이 아닌 사원의 사원번호, 이름, 해당관리자를 검색하라. (is not NULL)
```sql
select empno, ename, mgr
from emp
where mgr is not NULL;
```

## 문자열 변환 StrConv 
- 1 : 문자열을 대문자로 변환 2 : 소문자 3 : 첫글자 대문자로
<질의49> 사원들의 이름을 소문자로 검색하라.
```sql
select strconv(ename,2) -- 2 : 소문자 (문자열,번호)
from emp;
```

## 데이터의 개수 반환 COUNT()
<질의57> 부서별 사원들의 인원수를 검색하라.
```sql
select count(*)
from emp
group by deptno;
```

## 그룹화  GROUP BY
- 부서별로 데이터를 요약. 한 부서의 데이터가 하나의 결과로 나오게 할 때 사용한다.
<질의58> 관리자별 사원들의 인원수를 검색하라.
```sql
select count(*) -- 같은 mgr별로 집계.
from emp
group by mgr; -- 관리자별 인원수가 관리자별로 하나의 결과값이 나온다. order by는 관리자별 결과가 여러 개로 나온다.
```
<질의62> 부서별 직무별 사원의 급여 합을 검색하라.
```sql
select sum(sal)
from emp
group by deptno, job;
```
<질의64> 부서별 직무별 사원들의 입사일자의 최곳값과 최젓값을 검색하라.
```sql
select max(hiredate), min(hiredate)
from emp
group by deptno, job;
```
<질의65> 부서별 사원들의 인원수를 인원수가 많은 순으로 검색하라.
```sql
select deptno, count(*)
from emp
group by deptno
order by count(*) desc;
```

## 표준 편차 stdev()
- 지정한 필드의 모든 값에 대한 표준 편차 반환
 
<질의60> 부서별 사원들의 급여의 표준편차를 검색하라.
```sql
select deptno, stdev(sal) -- stdev는 집계 함수지만 group by로 그룹을 나누었기에 개별행을 추출하는 deptno과 사용 가능.
from emp
group by deptno;
```

## 자기 자신과 join (Self Join)
- 사원의 상급자 번호로 상사사원 이름을 찾아야 한다. 그러면 자기자신과 같은 테이블을 만들어서 그 테이블에서 추출해야 했다. 
- 자기 자신을 join하여 같은 테이블 두 개 사용해서 추출.
<질의87> 각 사원의 사원번호와 이름, 상급자 사원번호와 이름을 검색하라.
```sql
select e.empno, e.ename, e.mgr, m.ename
from emp e, emp m
where e.mgr = m.empno; -- 사원의 상급자 번호 = 다른 테이블의 사원 번호로 추출
```

## 서브쿼리 중첩
- ``>`` 하나의 값과 비교를 해야한다.
- 모든 부서들의 평균을 구해야 하니, 부서들의 평균 ``avg(sal)``을 하고 각 부서들의 평균까지 구한다. (서브쿼리 중첩)
<질의98> 급여가 모든 부서들의 평균 급여보다 많은 사원 정보를 검색하라.
```sql
SELECT *
FROM emp
WHERE sal > (
    SELECT AVG(avg_sal) -- 모든 부서의 평균
    FROM ( -- 또 다른 테이블을 참조.
        SELECT AVG(sal) AS avg_sal -- 밖에서 참조하게 별칭 사용.
        FROM emp
        GROUP BY deptno
    ) AS sub -- 서브쿼리를 from에 쓸 때 반드시 별칭을 부여해야 한다.
);
```

## HAVING 
- 집계 함수를 포함한 조건문
- 그룹을 만든 후 필터링한다.
<질의99> 20번 부서의 최대 급여보다 최대 급여가 큰 부서의 번호와, 최대 급여를 검색하라.
```sql
SELECT deptno, MAX(sal) AS max_sal -- AS max_sal은 별칭. Expr1000 같은 이름이 자동 생성하기 전에 가독성을 위해 만듦. 필수는 아니다.
FROM emp
GROUP BY deptno
HAVING MAX(sal) > ( -- GROUP 만든 후 HAVING으로 필터링 그룹들의 최대 급여와 비교.
    SELECT MAX(sal)
    FROM emp
    WHERE deptno = 20
);
```
