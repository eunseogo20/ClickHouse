---
machine_translated : true
machine_translated_rev : 71d72c1f237f4a553fe91ba6c6c633e81a49e35b
---

# SimpleAggregateFunction {# data-type-simpleaggregatefunction}

`SimpleAggregateFunction (name, types_of_arguments ...)`데이터 형식은 집계 함수의 현재 값을 저장하고 완전한 상태를 다음과 같이 저장하지 않습니다 [`AggregateFunction` (../../ sql-reference /data-types/aggregatefunction.md) 그렇다이 최적화는 다음의 속성이 유지되는 함수에 적용 할 수 있습니다. `f` 행 집합에`S1 UNION ALL S2` 얻을 수있어`f` 행의 일부에 별도로 설정하고 다시 적용합니다`f` 결과 :`f (S1 UNION ALL S2) = f (f (S1) UNION ALL f (S2))`이 속성은 부분 집계 결과가 결합 된 결과를 계산하기에 충분하다는 것을 보장하기 위해 여분의 데이터를 저장하고 처리 할 필요가 수

다음 집계 함수가 지원됩니다 :

- [`any` (../../ sql-reference / aggregate-functions / reference.md # agg_function-any)
- [`anyLast` (../../ sql-reference / aggregate-functions / reference.md # anylastx)
- [`min` (../../ sql-reference / aggregate-functions / reference.md # agg_function-min)
- [`max` (../../ sql-reference / aggregate-functions / reference.md # agg_function-max)
- [`sum` (../../ sql-reference / aggregate-functions / reference.md # agg_function-sum)
- [`groupBitAnd` (../../ sql-reference / aggregate-functions / reference.md # groupbitand)
- [`groupBitOr` (../../ sql-reference / aggregate-functions / reference.md # groupbitor)
- [`groupBitXor` (../../ sql-reference / aggregate-functions / reference.md # groupbitxor)
- [`groupArrayArray` (../../ sql-reference / aggregate-functions / reference.md # agg_function-grouparray)
- [`groupUniqArrayArray` (../../ sql-reference / aggregate-functions / reference.md # groupuniqarrayx-groupuniqarraymax-sizex)

값`SimpleAggregateFunction (func, Type)`보고 똑같이 저장`Type` 따라서 다음 함수를 적용 할 필요가 없습니다`-Merge` /`-State` 접미사. `SimpleAggregateFunction` 이상의 성능`AggregateFunction` 같은 집계 기능을 사용합니다.

** 파라미터 **

- 집계 함수의 이름입니다.
- 집계 함수의 인수 형식입니다.

** 예 **

```sql
CREATE TABLE t
(
    column1 SimpleAggregateFunction (sum, UInt64)
    column2 SimpleAggregateFunction (any, String)
) ENGINE = ...
```

원본 기사 (https://clickhouse.tech/docs/en/data_types/simpleaggregatefunction/) <! - hide ->
