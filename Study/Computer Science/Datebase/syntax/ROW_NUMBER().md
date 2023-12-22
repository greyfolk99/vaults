#sql #syntax #mariadb #mysql 

row별로 순차적으로 번호를 부여합니다. 아래 예제는 tb_purchasebill 테이블에서 BillNo 순서대로 번호를 부여하여 표시하도록 합니다.

```sql
SELECT 
	ROW_NUMBER() over(order by BillNo asc) as number, 
	BusinessName 
from tb_purchasebill tp;
```

전체적인 순서가 아닌 그룹별로 순서를 표시하고자 한다면 partition을 사용해야 합니다. 따라서 다음 쿼리는 BusinessName별로 각각 순번을 부여하게 됩니다.

```sql
select row_number () over(partition by tp.BusinessName order by tp.BillNo asc) as number, tp.BusinessName 
from tb_purchasebill tp;
```