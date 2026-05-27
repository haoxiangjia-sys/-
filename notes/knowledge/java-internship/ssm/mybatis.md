---
created: 2026-05-21
tags:
  - java/internship
  - java/ssm
related:
  - "[[../ssm]]"
  - "[[springmvc]]"
  - "[[../../sql/ddl]]"
  - "[[../../sql/dml]]"
  - "[[../../sql/dql]]"
---

# MyBatis

**MyBatis = ORM 框架，操作数据库**

## 核心配置

```xml
<!-- mybatis-config.xml -->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/example/UserMapper.xml"/>
    </mappers>
</configuration>
```

## 映射文件

```xml
<!-- UserMapper.xml -->
<mapper namespace="com.example.UserMapper">

    <!-- 查 -->
    <select id="selectById" resultType="com.example.User">
        SELECT * FROM user WHERE id = #{id}
    </select>

    <!-- 增 -->
    <insert id="insert" useGeneratedKeys="true" keyProperty="id">
        INSERT INTO user(name, age) VALUES(#{name}, #{age})
    </insert>

    <!-- 改 -->
    <update id="update">
        UPDATE user SET name = #{name} WHERE id = #{id}
    </update>

    <!-- 删 -->
    <delete id="delete">
        DELETE FROM user WHERE id = #{id}
    </delete>
</mapper>
```

## `#{}` vs `${}`

| 占位符 | 说明 | 防 SQL 注入 |
| ----- | ---- | ---------- |
| `#{name}` | 预编译，参数用 `?` 替代 | ✅ 安全 |
| `${name}` | 字符串拼接，直接替换 | ❌ 不安全（表名/列名动态时用） |

## 结果映射

```xml
<!-- 解决字段名不一致 -->
<resultMap id="userMap" type="com.example.User">
    <id column="id" property="id"/>
    <result column="user_name" property="name"/>
    <result column="user_age" property="age"/>
</resultMap>

<select id="selectById" resultMap="userMap">
    SELECT * FROM user WHERE id = #{id}
</select>
```

## 动态 SQL

```xml
<!-- if -->
<select id="list" resultType="User">
    SELECT * FROM user WHERE 1=1
    <if test="name != null and name != ''">
        AND name LIKE #{name}
    </if>
</select>

<!-- foreach -->
<select id="listByIds" resultType="User">
    SELECT * FROM user WHERE id IN
    <foreach collection="ids" item="id" open="(" separator="," close=")">
        #{id}
    </foreach>
</select>

<!-- sql 片段 -->
<sql id="columns">id, name, age</sql>
<select id="selectById" resultType="User">
    SELECT <include refid="columns"/> FROM user WHERE id = #{id}
</select>
```

## 注解方式（简单 SQL 推荐）

```java
public interface UserMapper {
    @Select("SELECT * FROM user WHERE id = #{id}")
    User selectById(int id);

    @Insert("INSERT INTO user(name, age) VALUES(#{name}, #{age})")
    @Options(useGeneratedKeys = true, keyProperty = "id")
    int insert(User user);

    @Update("UPDATE user SET name = #{name} WHERE id = #{id}")
    int update(User user);

    @Delete("DELETE FROM user WHERE id = #{id}")
    int delete(int id);
}
```

> **XML vs 注解**：复杂 SQL 用 XML（动态 SQL），简单 CRUD 用注解

---

**相关笔记**: [[../ssm]] · [[springmvc]] · [[springboot]] · [[../../sql/ddl]] · [[../../sql/dml]]
