### MySQL - join



select A.name,B.name from students as A, students as B;

列出所有可能的学生组合对,, 包括自己和自己的组合.

select A.name,B.name from students as A, students as B where A.name!=B.name;

这个里面就排除了自己和自己组合的方式.

