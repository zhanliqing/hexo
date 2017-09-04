---
title: mysql row colome transpose
date: 2017-07-31 11:01:21
tags:
---

CREATE TABLE score
(
    NAME CHAR(10),
    course CHAR(10),
    score INT
)

INSERT INTO score VALUES('张三','语文',80);
INSERT INTO score VALUES('张三','数学',81);
INSERT INTO score VALUES('张三','英语',82);
INSERT INTO score VALUES('张三','化学',83);
INSERT INTO score VALUES('李四','语文',80);
INSERT INTO score VALUES('李四','数学',81);
INSERT INTO score VALUES('李四','英语',82);
INSERT INTO score VALUES('王五','数学',83);
INSERT INTO score VALUES('王五','英语',80);

SELECT * FROM score

SELECT NAME ,
SUM(IF(course="语文",score,0)) AS '语文',
SUM(IF(course='数学',score,0)) AS '数学',
SUM(IF(course='英语',score,0)) AS '英语',
SUM(IF(course='化学',score,0)) AS '化学'
FROM score GROUP BY NAME



SELECT NAME ,
SUM(CASE course WHEN '语文' THEN  score ELSE 0 END) AS '语文',
SUM(CASE course WHEN '数学' THEN  score ELSE 0 END) AS '数学',
SUM(CASE course WHEN '英语' THEN  score ELSE 0 END) AS '英语',
SUM(CASE course WHEN '化学' THEN  score ELSE 0 END) AS '化学'
FROM score GROUP BY NAME


SELECT NAME ,
MAX(CASE course WHEN '语文' THEN  score ELSE 0 END) AS '语文',
MAX(CASE course WHEN '数学' THEN  score ELSE 0 END) AS '数学',
MAX(CASE course WHEN '英语' THEN  score ELSE 0 END) AS '英语',
MAX(CASE course WHEN '化学' THEN  score ELSE 0 END) AS '化学'
FROM score GROUP BY NAME

UPDATE score SET teacher='张三_语文老师' WHERE NAME='张三' AND course='语文';
UPDATE score SET teacher='张三_数学老师' WHERE NAME='张三' AND course='数学';
UPDATE score SET teacher='张三_英语老师' WHERE NAME='张三' AND course='英语';
UPDATE score SET teacher='张三_化学老师' WHERE NAME='张三' AND course='化学';
UPDATE score SET teacher='李四_语文老师' WHERE NAME='李四' AND course='语文';
UPDATE score SET teacher='李四_数学老师' WHERE NAME='李四' AND course='数学';
UPDATE score SET teacher='李四_英语老师' WHERE NAME='李四' AND course='英语';
UPDATE score SET teacher='王五_数学老师' WHERE NAME='王五' AND course='数学';
UPDATE score SET teacher='王五_英语老师' WHERE NAME='王五' AND course='英语';