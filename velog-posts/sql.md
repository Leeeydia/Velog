<pre><code class="language-sql"># a5 데이터베이스 삭제/생성/선택
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
</code></pre>