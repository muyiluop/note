# 一些记录

- 1、将一对多的多条记录合并成一条记录，使用 `group_concat(distinct field ORDER BY field)`

```sql
-- 例如
-- 原Sql
SELECT
 b.accountId,
  c.sno,
  c.imei,
  d.valveStaus,
  d.readNum
FROM
    water_user_device b
LEFT JOIN dev_device c ON b.sno = c.sno
LEFT JOIN dev_device_info d ON d.sno = c.sno
WHERE
    b.accountId = '4600000174';

-- 查询结果
-- 4600000174   805571207273   863703036754773   1   0.1000000000
-- 4600000174   805571207206   863703036768179   1   0.1000000000
-- 4600000174   805571206903   863703036544190   2   0.3000000000

-- 合并sql
SELECT
 b.accountId,
  group_concat(distinct c.sno ORDER BY c.sno),
  group_concat(distinct c.imei ORDER BY c.sno),
  group_concat(d.valveStaus ORDER BY c.sno),
  group_concat(d.readNum ORDER BY c.sno)
FROM
    water_user_device b
LEFT JOIN dev_device c ON b.sno = c.sno
LEFT JOIN dev_device_info d ON d.sno = c.sno
WHERE
    b.accountId = '4600000174'
GROUP BY b.accountId;

-- 查询结果
-- 4600000174   805571206903,805571207206,805571207273   863703036544190,863703036768179,863703036754773   2,1,1   0.3000000000,0.1000000000,0.1000000000
```

> 注意：使用内部排序来保证合并后的数据顺序能够对上。