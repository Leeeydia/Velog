<h4 id="1번">1번</h4>
<pre><code># a5 데이터베이스 삭제/생성/선택
DROP DATABASE IF EXISTS `a5`;

CREATE DATABASE `a5`;

USE `a5`;

# 부서(dept) 테이블 생성 및 홍보부서 기획부서 추가
CREATE TABLE dept(
id INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
PRIMARY KEY(id),
regDate DATETIME NOT NULL,
`name` CHAR(100) NOT NULL
);

INSERT INTO dept 
SET regDate = NOW(),
`name` = '홍보';

INSERT INTO dept 
SET regDate = NOW(),
`name` = '기획';

DESC dept;


# 사원(emp) 테이블 생성 및 홍길동사원(홍보부서), 홍길순사원(홍보부서), 임꺽정사원(기획부서) 추가
CREATE TABLE emp (
id INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
PRIMARY KEY(id),
regDate DATETIME NOT NULL,
`name` CHAR(100) NOT NULL,
deptName CHAR(100) NOT NULL
);

DESC dept;

# 홍보를 마케팅으로 변경


# 마케팅을 홍보로 변경

# 홍보를 마케팅으로 변경
# 구조를 변경하기로 결정(사원 테이블에서, 이제는 부서를 이름이 아닌 번호로 기억)

# 사장님께 드릴 인명록을 생성
SELECT * FROM emp;

# 사장님께서 부서번호가 아니라 부서명을 알고 싶어하신다.
# 그래서 dept 테이블 조회법을 알려드리고 혼이 났다.
SELECT `name`
FROM emp
INNER JOIN dept;
# 사장님께 드릴 인명록을 생성(v2, 부서명 포함, ON 없이)
# 이상한 데이터가 생성되어서 혼남
SELECT emp, *. dept.name AS `부서명`
FROM epm
INNER JOIN depo;
# 사장님께 드릴 인명록을 생성(v3, 부서명 포함, 올바른 조인 룰(ON) 적용)
# 보고용으로 좀 더 편하게 보여지도록 고쳐야 한다고 지적받음
SELECT emp.*, dept.name AS `부서명`
FROM emp
INNER JOIN dept
ON dept.id = emp.deptId;
# 사장님께 드릴 인명록을 생성(v4, 사장님께서 보시기에 편한 칼럼명(AS))

SELECT emp.id AS '사원번호', DATE(emp.regDate) AS '입사일', emp.name AS '사원명', dept.name AS `부서명`
FROM emp
INNER JOIN dept
ON dept.id = emp.deptId
ORDER BY `qntjaud`;
</code></pre><h4 id="2번">2번</h4>
<pre><code class="language-sql">DROP DATABASE IF EXISTS `mall`;

CREATE DATABASE mall;

USE mall;


CREATE TABLE t_shopping(
id INT(5) PRIMARY KEY AUTO_INCREMENT,
userId CHAR(30) NOT NULL,
userPw CHAR(30) NOT NULL,
userName CHAR(30) NOT NULL,
address CHAR(50) NOT NULL,
pname CHAR(50) NOT NULL,
price INT(5) NOT NULL
);

INSERT INTO t_shopping 
SET userId = 'user1',
userPw = 'pass1',
userName = '손흥민',
address = '런던',
pname = '운동화',
price = 1000000;

INSERT INTO t_shopping 
SET userId = 'user2',
userPw = 'pass2',
userName = '설현',
address = '서울',
pname = '코트',
price = 100000;

INSERT INTO t_shopping 
SET userId = 'user3',
userPw = 'pass3',
userName = '원빈',
address = '대전',
pname = '반바지',
price = 30000;

INSERT INTO t_shopping 
SET userId = 'user4',
userPw = 'pass4',
userName = '송혜교',
address = '대구',
pname = '스커트',
price = 15000;

INSERT INTO t_shopping 
SET userId = 'user5',
userPw = 'pass5',
userName = '소지섭',
address = '부산',
pname = '코트',
price = 100000;

INSERT INTO t_shopping 
SET userId = 'user6',
userPw = 'pass6',
userName = '김지원',
address = '울산',
pname = '티셔츠',
price = 9000;

INSERT INTO t_shopping 
SET userId = 'user6',
userPw = 'pass6',
userName = '김지원',
address = '울산',
pname = '운동화',
price = 200000;

INSERT INTO t_shopping 
SET userId = 'user1',
userPw = 'pass1',
userName = '손흥민',
address = '런던',
pname = '코트',
price = 100000;

INSERT INTO t_shopping 
SET userId = 'user4',
userPw = 'pass4',
userName = '송혜교',
address = '울산',
pname = '스커트',
price = 15000;

INSERT INTO t_shopping 
SET userId = 'user1',
userPw = 'pass1',
userName = '손흥민',
address = '런던',
pname = '운동화',
price = 1000000;

INSERT INTO t_shopping 
SET userId = 'user5',
userPw = 'pass5',
userName = '소지섭',
address = '부산',
pname = '모자',
price = 30000;

SELECT * FROM t_shopping;
# 손흥민의 주문 개수
SELECT COUNT(*) FROM t_shopping WHERE userName = '손흥민';
# 손흥민이 산 상품
SELECT DISTINCT pname FROM t_shopping WHERE userName = '손흥민';  #DISTINCT 중복 없애줌 
# 스커트를 산 사람은 ?
SELECT userId,userName FROM t_shopping WHERE pname = '스커트'; # 그러면 만약 데이터가 많으면 ? 어떻게 처리해야하는지? 아 똑같나
# 가장 많이 주문한 사람의 아이디와 이름 # 그룹함수 
SELECT COUNT(*) FROM t_shoping
SELECT * FROM t_shopping

SELECT userId,userName, COUNT(*) FROM t_shopping GROUP BY userName ORDER BY COUNT(*) DESC LIMIT 1;

# 소지섭이 사용한 총 금액
SELECT SUM(price)  FROM t_shopping WHERE userName = '소지섭';
</code></pre>
<h4 id="3번">3번</h4>
<pre><code>DROP DATABASE IF EXISTS `mall`;

CREATE DATABASE mall;

USE mall;

CREATE TABLE t_order(
id INT(5) PRIMARY KEY AUTO_INCREMENT,
userNo INT(5) NOT NULL,
productNo INT(5) NOT NULL
);

CREATE TABLE t_user(
id INT(5) PRIMARY KEY AUTO_INCREMENT,
userId CHAR(200) NOT NULL,
userPw CHAR(200) NOT NULL,
userName CHAR(50) NOT NULL,
addr CHAR(200) NOT NULL
);

CREATE TABLE t_product(
id INT(5) PRIMARY KEY AUTO_INCREMENT,
pname CHAR(100) NOT NULL,
price INT(10) NOT NULL
);


INSERT INTO t_product
SET pname = '운동화',
price = 1000000;

INSERT INTO t_product
SET pname = '코트',
price = 100000;

INSERT INTO t_product
SET pname = '반바지',
price = 30000;

INSERT INTO t_product
SET pname = '스커트',
price = 15000;

INSERT INTO t_product
SET pname = '코트',
price = 100000;

INSERT INTO t_product
SET pname = '티셔츠',
price = 9000;

INSERT INTO t_product
SET pname = '운동화',
price = 200000;

INSERT INTO t_product
SET pname = '모자',
price = 30000;



INSERT INTO t_user
SET userId = 'user1',
userPw = 'pass1',
userName = '손흥민',
addr = '런던';

INSERT INTO t_user
SET userId = 'user2',
userPw = 'pass2',
userName = '설현',
addr = '서울';

INSERT INTO t_user
SET userId = 'user3',
userPw = 'pass3',
userName = '원빈',
addr = '대전';

INSERT INTO t_user
SET userId = 'user4',
userPw = 'pass4',
userName = '송혜교',
addr = '대구';

INSERT INTO t_user
SET userId = 'user5',
userPw = 'pass5',
userName = '소지섭',
addr = '부산';

INSERT INTO t_user
SET userId = 'user6',
userPw = 'pass6',
userName = '김지원',
addr = '울산';


INSERT INTO t_order
SET userNo = 1,
productNo = 1;

INSERT INTO t_order
SET userNo = 2,
productNo = 2;

INSERT INTO t_order
SET userNo = 3,
productNo = 3;

INSERT INTO t_order
SET userNo = 4,
productNo = 4;

INSERT INTO t_order
SET userNo = 5,
productNo = 5;

INSERT INTO t_order
SET userNo = 6,
productNo = 6;

INSERT INTO t_order
SET userNo = 6,
productNo = 7;

INSERT INTO t_order
SET userNo = 1,
productNo = 5;

INSERT INTO t_order
SET userNo = 4,
productNo = 4;

INSERT INTO t_order
SET userNo = 1,
productNo = 1;

INSERT INTO t_order
SET userNo = 5,
productNo = 8;

SELECT * FROM t_order;
SELECT * FROM t_product;
SELECT * FROM t_order;

SELECT * FROM t_user
WHERE userName = '손흥민';



# 1. 손흥민의 주문 개수는? ???

SELECT COUNT(*) FROM t_order,t_user WHERE userName = '손흥민';

#풀이 (join (x))
SELECT * FROM t_order;
SELECT * FROM t_product;
SELECT * FROM t_order;

SELECT * FROM t_user
WHERE userName = '손흥민'

SELECT * FROM t_order
WHERE userNo = 1;

# 풀이 2 ( join o )


# 2. 손흥민이 산 상품은? ???

SELECT DISTINCT pname FROM t_user,t_order,t_product WHERE userName = '손흥민';




# 3. 스커트를 산 사람은? ???

SELECT pname FROM t_user,t_order,t_product WHERE pname = '스커트';

\
SELECT * FROM t_order;
SELECT * FROM t_product;
SELECT * FROM t_user;

SELECT * FROM t_product
WHERE pname = '스커트';



# 4. 가장 많이 주문한 사람의 아이디와 이름, 주문개수는? ???

SELECT * FROM t_order;
SELECT * FROM t_product;
SELECT * FROM t_user;

SELECT userNo, COUNT(userNo)
FROM t_order
GROUP BY userNo
ORDER ny COUNT(userNo) DESC LIMIT 1;

SELECT userName, userId FROM t_user WHERE id = 1;




# 5. 소지섭이 사용한 총 금액은? ???

SELECT * FROM t_order;
SELECT * FROM t_product;
SELECT * FROM t_user;
SELECT * FROM t_user

WHERE userName  = '소지섭';

SELECT * FROM t_order
WHERE userNo =5;

SELECT SUM(price) FROM t_product
WHERE id IN(5,8) 

</code></pre><h4 id="4번">4번</h4>
<pre><code># a6 DB 삭제/생성/선택
DROP DATABASE IF EXISTS `a6`;
CREATE DATABASE `a6`;
USE `a6`;

# 부서(홍보, 기획)
CREATE TABLE dept(
id INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
PRIMARY KEY(id),
regDate DATETIME NOT NULL,
`name` CHAR(100) NOT NULL,
deptId INT(10) UNSIGNED NOT NULL,
salary INT(10) UNSIGNED NOT NULL
);

INSERT INTO dept
SET regDate = NOW(),
`name` = '홍보';

INSERT INTO dept
SET regDate = NOW(),
`name` = '기획';

DESC dept

SELECT * FROM dept;

# 사원(홍길동/홍보/5000만원, 홍길순/홍보/6000만원, 임꺽정/기획/4000만원)

CREATE TABLE emp (
id INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
PRIMARY KEY(id),
regDate DATETIME NOT NULL,
`name` CHAR(100) NOT NULL,
`user` CHAR(100) NOT NULL,
deptId INT(10) NOT NULL,
salary INT(10) NOT NULL
);

INSERT INTO emp
SET regDate = NOW(),
`name` = '홍보',
`user` = '홍길동',
deptId = 1,
salary = 5000 ;

INSERT INTO emp
SET regDate = NOW(),
`name` = '홍보',
`user` = '홍길순',
deptId = 2,
salary = 6000;

INSERT INTO emp
SET regDate = NOW(),
`name` = '기획',
`user` = '임꺽정',
deptId = 3,
salary = 4000 ;


# 사원 수 출력
SELECT * FROM emp;
# 가장 큰 사원 번호 출력
SELECT MAX(id) FROM emp;
# 가장 고액 연봉

# 가장 저액 연봉


# 회사에서 1년 고정 지출(인건비)

SELECT SUM(salary) 'salary'
FROM emp

# 부서별, 1년 고정 지출(인건비)`
SELECT SUM (salary 'salary'
FROM emp
GROUP BY `name`
ORDER BY COUNT(*) DESC
LIMIT 1; 

# 부서별, 최고연봉


# 부서별, 최저연봉


# 부서별, 평균연봉


# 부서별, 부서명, 사원리스트, 평균연봉, 최고연봉, 최소연봉, 사원수 
## V1(조인 안한 버전)

## V2(조인해서 부서명까지 나오는 버전)

## V3(V2에서 평균연봉이 5000이상인 부서로 추리기)

## V4(V3에서 HAVING 없이 서브쿼리로 수행)
### HINT, UNION을 이용한 서브쿼리
# SELECT *
# FROM (
#     select 1 AS id
#     union
#     select 2
#     UNION
#     select 3
# ) AS A</code></pre><h4 id="5번">5번</h4>
<pre><code>DROP DATABASE IF EXISTS scott;

CREATE DATABASE scott;

USE scott;

CREATE TABLE DEPT (
    DEPTNO DECIMAL(2),
    DNAME VARCHAR(14),
    LOC VARCHAR(13),
    CONSTRAINT PK_DEPT PRIMARY KEY (DEPTNO) 
);
CREATE TABLE EMP (
    EMPNO DECIMAL(4),
    ENAME VARCHAR(10),
    JOB VARCHAR(9),
    MGR DECIMAL(4),
    HIREDATE DATE,
    SAL DECIMAL(7,2),
    COMM DECIMAL(7,2),
    DEPTNO DECIMAL(2),
    CONSTRAINT PK_EMP PRIMARY KEY (EMPNO),
    CONSTRAINT FK_DEPTNO FOREIGN KEY (DEPTNO) REFERENCES DEPT(DEPTNO)
);
CREATE TABLE SALGRADE ( 
    GRADE TINYINT,
    LOSAL SMALLINT,
    HISAL SMALLINT 
);
INSERT INTO DEPT VALUES (10,'ACCOUNTING','NEW YORK');
INSERT INTO DEPT VALUES (20,'RESEARCH','DALLAS');
INSERT INTO DEPT VALUES (30,'SALES','CHICAGO');
INSERT INTO DEPT VALUES (40,'OPERATIONS','BOSTON');
INSERT INTO EMP VALUES (7369,'SMITH','CLERK',7902,STR_TO_DATE('17-12-1980','%d-%m-%Y'),800,NULL,20);
INSERT INTO EMP VALUES (7499,'ALLEN','SALESMAN',7698,STR_TO_DATE('20-2-1981','%d-%m-%Y'),1600,300,30);
INSERT INTO EMP VALUES (7521,'WARD','SALESMAN',7698,STR_TO_DATE('22-2-1981','%d-%m-%Y'),1250,500,30);
INSERT INTO EMP VALUES (7566,'JONES','MANAGER',7839,STR_TO_DATE('2-4-1981','%d-%m-%Y'),2975,NULL,20);
INSERT INTO EMP VALUES (7654,'MARTIN','SALESMAN',7698,STR_TO_DATE('28-9-1981','%d-%m-%Y'),1250,1400,30);
INSERT INTO EMP VALUES (7698,'BLAKE','MANAGER',7839,STR_TO_DATE('1-5-1981','%d-%m-%Y'),2850,NULL,30);
INSERT INTO EMP VALUES (7782,'CLARK','MANAGER',7839,STR_TO_DATE('9-6-1981','%d-%m-%Y'),2450,NULL,10);
INSERT INTO EMP VALUES (7788,'SCOTT','ANALYST',7566,STR_TO_DATE('13-7-1987','%d-%m-%Y')-85,3000,NULL,20);
INSERT INTO EMP VALUES (7839,'KING','PRESIDENT',NULL,STR_TO_DATE('17-11-1981','%d-%m-%Y'),5000,NULL,10);
INSERT INTO EMP VALUES (7844,'TURNER','SALESMAN',7698,STR_TO_DATE('8-9-1981','%d-%m-%Y'),1500,0,30);
INSERT INTO EMP VALUES (7876,'ADAMS','CLERK',7788,STR_TO_DATE('13-7-1987', '%d-%m-%Y'),1100,NULL,20);
INSERT INTO EMP VALUES (7900,'JAMES','CLERK',7698,STR_TO_DATE('3-12-1981','%d-%m-%Y'),950,NULL,30);
INSERT INTO EMP VALUES (7902,'FORD','ANALYST',7566,STR_TO_DATE('3-12-1981','%d-%m-%Y'),3000,NULL,20);
INSERT INTO EMP VALUES (7934,'MILLER','CLERK',7782,STR_TO_DATE('23-1-1982','%d-%m-%Y'),1300,NULL,10);
INSERT INTO SALGRADE VALUES (1,700,1200);
INSERT INTO SALGRADE VALUES (2,1201,1400);
INSERT INTO SALGRADE VALUES (3,1401,2000);
INSERT INTO SALGRADE VALUES (4,2001,3000);
INSERT INTO SALGRADE VALUES (5,3001,9999);


#1. 사원 테이블의 모든 레코드를 조회하시오.


#2. 사원명과 입사일을 조회하시오.


#3. 사원번호와 이름을 조회하시오.


#4. 사원테이블에 있는 직책의 목록을 조회하시오. (hint : distinct, group by)


#5. 총 사원수를 구하시오. (hint : count)


#6. 부서번호가 10인 사원을 조회하시오.


#7. 월급여가 2500이상 되는 사원을 조회하시오.


#8. 이름이 'KING'인 사원을 조회하시오.


#9. 사원들 중 이름이 S로 시작하는 사원의 사원번호와 이름을 조회하시오. (hint : like)


#10. 사원 이름에 T가 포함된 사원의 사원번호와 이름을 조회하시오. (hint : like)


#11. 커미션이 300, 500, 1400 인 사원의 사번,이름,커미션을 조회하시오. (hint : OR, in )


#12. 월급여가 1200 에서 3500 사이의 사원의 사번,이름,월급여를 조회하시오. (hint : AND, between)


#13. 직급이 매니저이고 부서번호가 30번인 사원의 이름,사번,직급,부서번호를 조회하시오. 


#14. 부서번호가 30인 아닌 사원의 사번,이름,부서번호를 조회하시오. (not)


#15. 커미션이 300, 500, 1400 이 모두 아닌 사원의 사번,이름,커미션을 조회하시오. (hint : not in)


#16. 이름에 S가 포함되지 않는 사원의 사번,이름을 조회하시오. (hint : not like)


#17. 급여가 1200보다 미만이거나 3700 초과하는 사원의 사번,이름,월급여를 조회하시오. (hint : not, between)


#18. 직속상사가 NULL 인 사원의 이름과 직급을 조회하시오. (hint : is null, is not null)


#19. 부서별 평균월급여를 구하는 쿼리 (hint : group by, avg())


#20. 부서별 전체 사원수와 커미션을 받는 사원들의 수를 구하는 쿼리 (hint : group by, count())


#21. 부서별 최대 급여와 최소 급여를 구하는 쿼리 (hint : group by, min(), max())


#22. 부서별로 급여 평균 (단, 부서별 급여 평균이 2000 이상만) (hint : group by, having)


#23. 월급여가 1000 이상인 사원만을 대상으로 부서별로 월급여 평균을 구하라. 단, 평균값이 2000 이상인 레코드만 구하라. (hint : group by, having)


#24. 사원명과 부서명을 조회하시오. (hint : inner join)


#25. 이름,월급여,월급여등급을 조회하시오. (hint : inner join, between)


#26. 이름,부서명,월급여등급을 조회하시오. 


#27. 이름,직속상사이름을 조회하시오. (hint : self join

#28. 이름,직속상사이름을 조회하시오.(단 직속 상사가 없는 사람도 직속상사 결과가 null값으로 나와야 함) (hint : outer join)
###외부OUTER 조인. A LEFT JOIN B는 조인 조건에 만족하지 못하더라도 왼쪽 테이블 A의 행을 나타내고 싶을 때 사용한다. 반대로 A RIGHT JOIN B는 조인 조건에 만족하지 못하더라도 오른쪽 테이블 B의 행을 나타내고 싶을 때

#29. 이름,부서명을 조회하시오.단, 사원테이블에 부서번호가 40에 속한 사원이 없지만 부서번호 40인 부서명도 출력되도록 하시오. (hint : outer join)



#-------------------------- 필수 #


#서브 쿼리는 SELECT 문 안에서 ()로 둘러싸인 SELECT 문을 말하며 쿼리문의 결과를 메인 쿼리로 전달하기 위해 사용된다.
#사원명 'JONES'가 속한 부서명을 조회하시오.
#부서번호를 알아내기 위한 쿼리가 서브 쿼리로 사용.

#30. 10번 부서에서 근무하는 사원의 이름과 10번 부서의 부서명을 조회하시오. (hint : sub query)

#31. 평균 월급여보다 더 많은 월급여를 받은 사원의 사원번호,이름,월급여 조회하시오. (hint : sub query)

#32. 부서번호가 10인 사원중에서 최대급여를 받는 사원의 사원번호, 이름을 조회하시오. (hint : sub query)


</code></pre><h4 id="과제">과제</h4>
<pre><code>DROP DATABASE IF EXISTS scott;

CREATE DATABASE scott;

USE scott;

CREATE TABLE DEPT (
    DEPTNO DECIMAL(2),
    DNAME VARCHAR(14),
    LOC VARCHAR(13),
    CONSTRAINT PK_DEPT PRIMARY KEY (DEPTNO) 
);
CREATE TABLE EMP (
    EMPNO DECIMAL(4),
    ENAME VARCHAR(10),
    JOB VARCHAR(9),
    MGR DECIMAL(4),
    HIREDATE DATE,
    SAL DECIMAL(7,2),
    COMM DECIMAL(7,2),
    DEPTNO DECIMAL(2),
    CONSTRAINT PK_EMP PRIMARY KEY (EMPNO),
    CONSTRAINT FK_DEPTNO FOREIGN KEY (DEPTNO) REFERENCES DEPT(DEPTNO)
);
CREATE TABLE SALGRADE ( 
    GRADE TINYINT,
    LOSAL SMALLINT,
    HISAL SMALLINT 
);
INSERT INTO DEPT VALUES (10,'ACCOUNTING','NEW YORK');
INSERT INTO DEPT VALUES (20,'RESEARCH','DALLAS');
INSERT INTO DEPT VALUES (30,'SALES','CHICAGO');
INSERT INTO DEPT VALUES (40,'OPERATIONS','BOSTON');
INSERT INTO EMP VALUES (7369,'SMITH','CLERK',7902,STR_TO_DATE('17-12-1980','%d-%m-%Y'),800,NULL,20);
INSERT INTO EMP VALUES (7499,'ALLEN','SALESMAN',7698,STR_TO_DATE('20-2-1981','%d-%m-%Y'),1600,300,30);
INSERT INTO EMP VALUES (7521,'WARD','SALESMAN',7698,STR_TO_DATE('22-2-1981','%d-%m-%Y'),1250,500,30);
INSERT INTO EMP VALUES (7566,'JONES','MANAGER',7839,STR_TO_DATE('2-4-1981','%d-%m-%Y'),2975,NULL,20);
INSERT INTO EMP VALUES (7654,'MARTIN','SALESMAN',7698,STR_TO_DATE('28-9-1981','%d-%m-%Y'),1250,1400,30);
INSERT INTO EMP VALUES (7698,'BLAKE','MANAGER',7839,STR_TO_DATE('1-5-1981','%d-%m-%Y'),2850,NULL,30);
INSERT INTO EMP VALUES (7782,'CLARK','MANAGER',7839,STR_TO_DATE('9-6-1981','%d-%m-%Y'),2450,NULL,10);
INSERT INTO EMP VALUES (7788,'SCOTT','ANALYST',7566,STR_TO_DATE('13-7-1987','%d-%m-%Y')-85,3000,NULL,20);
INSERT INTO EMP VALUES (7839,'KING','PRESIDENT',NULL,STR_TO_DATE('17-11-1981','%d-%m-%Y'),5000,NULL,10);
INSERT INTO EMP VALUES (7844,'TURNER','SALESMAN',7698,STR_TO_DATE('8-9-1981','%d-%m-%Y'),1500,0,30);
INSERT INTO EMP VALUES (7876,'ADAMS','CLERK',7788,STR_TO_DATE('13-7-1987', '%d-%m-%Y'),1100,NULL,20);
INSERT INTO EMP VALUES (7900,'JAMES','CLERK',7698,STR_TO_DATE('3-12-1981','%d-%m-%Y'),950,NULL,30);
INSERT INTO EMP VALUES (7902,'FORD','ANALYST',7566,STR_TO_DATE('3-12-1981','%d-%m-%Y'),3000,NULL,20);
INSERT INTO EMP VALUES (7934,'MILLER','CLERK',7782,STR_TO_DATE('23-1-1982','%d-%m-%Y'),1300,NULL,10);
INSERT INTO SALGRADE VALUES (1,700,1200);
INSERT INTO SALGRADE VALUES (2,1201,1400);
INSERT INTO SALGRADE VALUES (3,1401,2000);
INSERT INTO SALGRADE VALUES (4,2001,3000);
INSERT INTO SALGRADE VALUES (5,3001,9999);


#1. 사원 테이블의 모든 레코드를 조회하시오.

SELECT * FROM EMP;


#2. 사원명과 입사일을 조회하시오.

SELECT JOB, HIREDATE FROM EMP;

#3. 사원번호와 이름을 조회하시오.

SELECT EMPNO, ENAME FROM EMP;
#4. 사원테이블에 있는 직책의 목록을 조회하시오. (hint : distinct, group by)

SELECT DISTINCT JOB FROM EMP;
#5. 총 사원수를 구하시오. (hint : count)

SELECT COUNT(*) FROM EMP;

#6. 부서번호가 10인 사원을 조회하시오.

SELECT * FROM EMP WHERE DEPTNO = 10;

#7. 월급여가 2500이상 되는 사원을 조회하시오.

SELECT * FROM emp WHERE SAL &gt;= 2500;

#8. 이름이 'KING'인 사원을 조회하시오.

SELECT * FROM EMP WHERE ENAME = 'KING';
#9. 사원들 중 이름이 S로 시작하는 사원의 사원번호와 이름을 조회하시오. (hint : like)

SELECT empno, ename FROM EMP WHERE ENAME LIKE 'S%';

#10. 사원 이름에 T가 포함된 사원의 사원번호와 이름을 조회하시오. (hint : like)

SELECT empno, ename FROM EMP WHERE ENAME LIKE '%T%';
#11. 커미션이 300, 500, 1400 인 사원의 사번,이름,커미션을 조회하시오. (hint : OR, in )

SELECT empno, ename FROM emp WHERE comm = 300 OR 500 OR 1400;
#12. 월급여가 1200 에서 3500 사이의 사원의 사번,이름,월급여를 조회하시오. (hint : AND, between)
SELECT empno, ename FROM emp WHERE comm BETWEEN 1200 AND 3500;

#13. 직급이 매니저이고 부서번호가 30번인 사원의 이름,사번,직급,부서번호를 조회하시오. 
SELECT empno, ename FROM emp WHERE job = 'MANAGER' AND deptno = 30;

#14. 부서번호가 30인 아닌 사원의 사번,이름,부서번호를 조회하시오. (not)

SELECT empno, ename, deptno FROM emp WHERE NOT deptno = 30;
#15. 커미션이 300, 500, 1400 이 모두 아닌 사원의 사번,이름,커미션을 조회하시오. (hint : not in)
SELECT empno, ename, deptno FROM emp WHERE comm NOT IN comm = 300, 500, 1400;

#16. 이름에 S가 포함되지 않는 사원의 사번,이름을 조회하시오. (hint : not like)


#17. 급여가 1200보다 미만이거나 3700 초과하는 사원의 사번,이름,월급여를 조회하시오. (hint : not, between)


#18. 직속상사가 NULL 인 사원의 이름과 직급을 조회하시오. (hint : is null, is not null)


#19. 부서별 평균월급여를 구하는 쿼리 (hint : group by, avg())


#20. 부서별 전체 사원수와 커미션을 받는 사원들의 수를 구하는 쿼리 (hint : group by, count())


#21. 부서별 최대 급여와 최소 급여를 구하는 쿼리 (hint : group by, min(), max())


#22. 부서별로 급여 평균 (단, 부서별 급여 평균이 2000 이상만) (hint : group by, having)


#23. 월급여가 1000 이상인 사원만을 대상으로 부서별로 월급여 평균을 구하라. 단, 평균값이 2000 이상인 레코드만 구하라. (hint : group by, having)


#24. 사원명과 부서명을 조회하시오. (hint : inner join)


#25. 이름,월급여,월급여등급을 조회하시오. (hint : inner join, between)


#26. 이름,부서명,월급여등급을 조회하시오. 


#27. 이름,직속상사이름을 조회하시오. (hint : self join

#28. 이름,직속상사이름을 조회하시오.(단 직속 상사가 없는 사람도 직속상사 결과가 null값으로 나와야 함) (hint : outer join)
###외부OUTER 조인. A LEFT JOIN B는 조인 조건에 만족하지 못하더라도 왼쪽 테이블 A의 행을 나타내고 싶을 때 사용한다. 반대로 A RIGHT JOIN B는 조인 조건에 만족하지 못하더라도 오른쪽 테이블 B의 행을 나타내고 싶을 때

#29. 이름,부서명을 조회하시오.단, 사원테이블에 부서번호가 40에 속한 사원이 없지만 부서번호 40인 부서명도 출력되도록 하시오. (hint : outer join)



#-------------------------- 필수 #


#서브 쿼리는 SELECT 문 안에서 ()로 둘러싸인 SELECT 문을 말하며 쿼리문의 결과를 메인 쿼리로 전달하기 위해 사용된다.
#사원명 'JONES'가 속한 부서명을 조회하시오.
#부서번호를 알아내기 위한 쿼리가 서브 쿼리로 사용.

#30. 10번 부서에서 근무하는 사원의 이름과 10번 부서의 부서명을 조회하시오. (hint : sub query)

#31. 평균 월급여보다 더 많은 월급여를 받은 사원의 사원번호,이름,월급여 조회하시오. (hint : sub query)

#32. 부서번호가 10인 사원중에서 최대급여를 받는 사원의 사원번호, 이름을 조회하시오. (hint : sub query)


</code></pre>