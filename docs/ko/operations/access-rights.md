---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 48
toc_title : "\ u30A2 \ u30AF \ u30BB \ u30B9 \ u5236 \ u5FA1 \ u3068 \ u30A2 \ u30AB \ u30A6 \ u30F3 \ u30C8 \
  \ u7BA1 \ u7406 "
---

# 액세스 제어 및 계정 관리 {# access-control}

ClickHouse는 기반 액세스 제어 관리를 지원합니다 [RBAC (https://en.wikipedia.org/wiki/Role-based_access_control) 접근

ClickHouse 액세스 사업체 :
- 사용자 (# user-account-management)
- [역할] (# role-management)
- 행 정책 (# row-policy-management)
- [설정 프로파일 (# settings-profiles-management)
- [할당량] (# quotas-management)

를 설정할 수 액세스를 사용했다 :

- SQL 기반 워크 플로우.

    해야합니다 [사용] (# enabling-access-control) 기능.

- 서버 설정 파일 (configuration-files.md)`users.xml`와`config.xml`.

SQL 기반 워크 플로우를 사용하는 것이 좋습니다. 두 구성 방법이 동시에 작동하므로 계정 및 권한을 관리하기 위해 서버 구성 파일을 사용하려면 sql 기반 워크 플로에 쉽게

!!! note "경고"
    두 구성 방식으로 동일한 access 요소를 동시에 관리 할 수 ​​없습니다.

## 사용법 {# access-control-usage}

기본적으로 ClickHouse 서버는 사용자 계정을 제공합니다`default` SQL 기반 액세스 제어 및 계정 관리를 사용할 수 없지만 모든 권한과 권한을 가지고 있습니다. 그`default` 사용자 계정은 클라이언트에서 로그인 및 분산 쿼리 등 사용자 이름이 정의되어 있지 않은 경우에 사용됩니다. 분산 쿼리 처리의 기본 사용자 계정을 사용하는 경우, 설정의 서버 또는 클러스터를 지정하지 않는 사용자 및 암호 (../ engines / table-engines / special / distributed.md) 속성.

ClickHouse의 사용을 시작한지 ​​얼마 안된 경우 다음 시나리오를 사용할 수 있습니다 :

1. 유효 함 (# enabling-access-control)를위한 SQL 기반 액세스 제어 및 계정 관리`default` 사용자.
2. 아래에서 로그인`default` 사용자 계정을 만들고 필요한 모든 사용자 관리자 계정 생성을 잊지 마세요 (`GRANT ALL ON *. * WITH GRANT OPTION TO admin_user_account`).
3. 권한의 제한 (settings / permissions-for-queries.md # permissions_for_queries)를 위해`default` SQL 기반 액세스 제어 및 계정 관리 사용자 및 비활성화합니다.

### 현재 솔루션의 특성 {# access-control-properties}

- 데이터베이스와 테이블이 존재하지 않는 경우에도 권한을 부여 할 수 있습니다.
- 테이블이 삭제 된 경우,이 테이블에 해당하는 모든 권한은 취소되지 않습니다. 따라서 나중에 같은 이름으로 새 테이블이 작성되면 모든 권한이 다시 사실입니다. 삭제 된 테이블에 해당하는 권한을 취소하려면 다음과 같이 실행해야합니다. `REVOKE ALL PRIVILEGES ON db.table FROM ALL` 쿼리.
- 권한의 유효 기간의 설정은 없습니다.

## 사용자 {# user-account-management}

사용자 계정은 ClickHouse 누군가를 승인 할 수있는 액세스 엔티티입니다. 사용자 아카우 :

- 신원
- [권한] (../ sql-reference / statements / grant.md # grant-privileges) 이것은 사용자가 실행할 수있는 쿼리의 범위를 정의합니다.
- ClickHouse 서버에 연결할 수있는 호스트.
- 부여 된 역할과 기본 역할.
- 사용자의 로그인시에 기본적으로 적용되는 제약 조건이있는 설정.
- 할당을 설정합니다.

사용자 계정에 대한 권한은 [GRANT (../ sql-reference / statements / grant.md) 쿼리 또는 할당 역할 (# role-management) 사용자로부터 권한을 취소하려면 ClickHouse은 [REVOKE ( ../sql-reference/statements/revoke.md) 쿼리. 사용자의 권한을 나열하려면 - [SHOW GRANTS] (../ sql-reference / statements / show.md # show-grants-statement) 계산서.

관리 쿼리 :

- CREATE USER] (../ sql-reference / statements / create.md # create-user-statement)
- ALTER USER] (../ sql-reference / statements / alter.md # alter-user-statement)
- [DROP USER] (../ sql-reference / statements / misc.md # drop-user-statement)
- [SHOW CREATE USER] (../ sql-reference / statements / show.md # show-create-user-statement)

### 설정 적용 {# access-control-settings-applying}

설정은 여러 가지 방법으로 설정할 수 있습니다. 사용자가 로그인 할 때 다른 액세스 엔티티에서 설정이 설정되어있는 경우,이 설정 값 및 제한 조건은 다음의 우선 순위에 따라 적용됩니다 () :

1. 사용자 계정 설정.
2. 사용자 계정의 기본 역할 설정. 일부 역할 설정이 설정되어있는 경우, 설정이 적용되는 순서는 정의되지 않습니다.
3. 사용자 또는 기본 역할에 할당 된 설정 프로파일 설정. 일부 프로파일 설정이 설정되어있는 경우, 설정 적용 순서는 정의되지 않습니다.
4. 모든 서버에 기본 또는 표준 프로필 (server-configuration-parameters / settings.md # default-profile).

## 역할 {# role-management}

Role는 사용자 계정에 부여 할 수 access 엔터티 컨테이너입니다.

롤 :

- [권한] (../ sql-reference / statements / grant.md # grant-privileges)
- 설정 및 제한
- 부여 된 역할 목록

관리 쿼리 :

- CREATE ROLE (../ sql-reference / statements / create.md # create-role-statement)
- ALTER ROLE (../ sql-reference / statements / alter.md # alter-role-statement)
- [DROP ROLE] (../ sql-reference / statements / misc.md # drop-role-statement)
- [SET ROLE] (../ sql-reference / statements / misc.md # set-role-statement)
- [SET DEFAULT ROLE] (../ sql-reference / statements / misc.md # set-default-role-statement)
- [SHOW CREATE ROLE (../ sql-reference / statements / show.md # show-create-role-statement)

역할에 대한 권한은 [GRANT (../ sql-reference / statements / grant.md) 쿼리. 롤 ClickHouse에서 특권을 취소하려면 [REVOKE (../ sql-reference / statements / revoke.md) 쿼리.

## 행 정책 {# row-policy-management}

행 정책은 사용자 또는 역할에서 사용할 수있는 행 또는 행을 정의하는 필터입니다. 행정 정책을 포함한 차에 대한 특정 테이블 목록의 역할 및 / 또는 사용자는이 행정 정책입니다.

관리 쿼리 :

- CREATE ROW POLICY] (../ sql-reference / statements / create.md # create-row-policy-statement)
- ALTER ROW POLICY] (../ sql-reference / statements / alter.md # alter-row-policy-statement)
- [DROP ROW POLICY] (../ sql-reference / statements / misc.md # drop-row-policy-statement)
- [SHOW CREATE ROW POLICY] (../ sql-reference / statements / show.md # show-create-row-policy-statement)

## 설정 프로파일 {# settings-profiles-management}

설정 프로필 설정 (settings / index.md) 설정 프로파일은 설정 및 제한 및 할당량이 적용되는 역할이나 사용자의 목록이 포함되어 있습니다.

관리 쿼리 :

- CREATE SETTINGS PROFILE] (../ sql-reference / statements / create.md # create-settings-profile-statement)
- ALTER SETTINGS PROFILE] (../ sql-reference / statements / alter.md # alter-settings-profile-statement)
- [DROP SETTINGS PROFILE] (../ sql-reference / statements / misc.md # drop-settings-profile-statement)
- [SHOW CREATE SETTINGS PROFILE] (../ sql-reference / statements / show.md # show-create-settings-profile-statement)

## 할당량 {# quotas-management}

할당량 제한 자원 이용에 볼 할당량 (quotas.md).

정원의 제한으로 인해 일부 시간 목록의 역할 및 / 또는 사용자는이 수량에 도달하면.

관리 쿼리 :

- CREATE QUOTA (../ sql-reference / statements / create.md # create-quota-statement)
- ALTER QUOTA (../ sql-reference / statements / alter.md # alter-quota-statement)
- [DROP QUOTA (../ sql-reference / statements / misc.md # drop-quota-statement)
- [SHOW CREATE QUOTA (../ sql-reference / statements / show.md # show-create-quota-statement)

## SQL 기반 액세스 제어 및 계정 관리 활성화 {# enabling-access-control}

- 설정 디렉토리 구성을 저장합니다.

    ClickHouse 액세스 엔티티 설정을 access_control_path (server-configuration-parameters / settings.md # access_control_path) 서버 구성 매개 변수.

- SQL 기반 액세스 제어 및 계정 관리를 활성화합니다.

    기본 SQL 형의 액세스 제어 및 특별 계좌의 계좌 관리 오의 모든 사용자 사용자를 설정해야합니다. `users.xml`에 1을 할당합니다. [access_management (settings / settings-users.md # access_management-user-setting) 설정.

원본 기사 (https://clickhouse.tech/docs/en/operations/access_rights/) <! - hide ->
