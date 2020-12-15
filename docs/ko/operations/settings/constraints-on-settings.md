---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 62
toc_title : "\ u8A2D \ u5B9A \ u306E \ u5236 \ u7D04"
---

# 설정의 제약 {# constraints-on-settings}

설정에 대한 제한 조건은 다음으로 정의 할 수 있습니다. `profiles` 섹션`user.xml` 설정 파일과 사용자가 설정 일부를 변경하는 것을 금지합니다. `SET` 쿼리.
제약은 다음과 같이 정의됩니다 :

```xml
<profiles>
  <user_name>
    <constraints>
      <setting_name_1>
        <min> lower_boundary </ min>
      </ setting_name_1>
      <setting_name_2>
        <max> upper_boundary </ max>
      </ setting_name_2>
      <setting_name_3>
        <min> lower_boundary </ min>
        <max> upper_boundary </ max>
      </ setting_name_3>
      <setting_name_4>
        <readonly />
      </ setting_name_4>
    </ constraints>
  </ user_name>
</ profiles>
```

사용자가 제약 조건을 위반하려고하면 예외가 발생하고 설정은 변경되지 않습니다.
지원되는 제약 조건은 다음과 같습니다 :`min``max``readonly` 그`min`와`max` 제약은 수치 설정의 상한과 하한을 지정하고 조합하여 사용할 수 있습니다. 그`readonly` 제약을 지정하면 사용자는 변경 불가능하므로 해당 설정입니다.

** 예 ** 자`users.xml` 행을 포함 :

```xml
<profiles>
  <default>
    <max_memory_usage> 10000000000 </ max_memory_usage>
    <force_index_by_date> 0 </ force_index_by_date>
    ...
    <constraints>
      <max_memory_usage>
        <min> 5000000000 </ min>
        <max> 20000000000 </ max>
      </ max_memory_usage>
      <force_index_by_date>
        <readonly />
      </ force_index_by_date>
    </ constraints>
  </ default>
</ profiles>
```

다음 쿼리 모든 예외를 throw :

```sql
SET max_memory_usage = 20000000001;
SET max_memory_usage = 4999999999;
SET force_index_by_date = 1;
```

```text
Code : 452, e.displayText () = DB :: Exception : Setting max_memory_usage should not be greater than 20,000,000,000.
Code : 452, e.displayText () = DB :: Exception : Setting max_memory_usage should not be less than 5000000000.
Code : 452, e.displayText () = DB :: Exception : Setting force_index_by_date should not be changed.
```

** 참고 : ** 그`default` 프로필에 특별한 처리가 있습니다. `default` 전문 디폴트의 제약이 제한하는 모든 사용자까지 그들의 방법을 명시 적으로 이러한 사용자

원본 기사 (https://clickhouse.tech/docs/en/operations/settings/constraints_on_settings/) <! - hide ->
