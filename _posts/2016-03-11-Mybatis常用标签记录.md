---
layout:     post
title:      Mybatis常用标签记录
subtitle:   Mybatis常用标签简单例子
date:       2016-03-11
author:     壹芝
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
    - Mybatis
    - Java
---

## 常用标签记录

###### 整理自本人的有道历史云笔记

#### 1. if condition

```
<if test="unionUserId != null and unionUserId != ''">
    and c.union_user_id = #{unionUserId}
</if>
```

#### 2. if else condition

```
<choose>
    <when test="queryModifyTime != null and queryModifyTime != ''">
        AND a.gmt_modify BETWEEN #{startTime} AND #{endTime}
    </when>
    <when test="">
    ////....
    </when>
    <otherwise>
        AND a.gmt_create BETWEEN #{startTime} AND #{endTime}
    </otherwise>
</choose>
```

#### 3. foreach condition

```
<delete id="deleteBatch"> 
　delete from user where id in
　<foreach collection="array" item="id" index="index" open="(" close=")" separator=",">
　　　　#{id}
　　</foreach>
</delete
```

#### 4. where

```
<select id="findOneUser"
     resultType="User">
  SELECT * FROM t_user
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

#### 5. set
```
<update id="updateUser">
  update t_user
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

#### 6. TypeHandler
```
<result column="phone" property="phone" typeHandler="xxx.xxx.xxx.typehandler.EncryptTypeHandler"/>

#{phone,typeHandler=xxx.xxx.xxx.typehandler.EncryptTypeHandler}


import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import org.apache.ibatis.type.BaseTypeHandler;
import org.apache.ibatis.type.JdbcType;
import org.apache.ibatis.type.MappedJdbcTypes;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

/**
 * 敏感字段加密
 *
 * @author  fangzheng
 */
@Slf4j
@MappedJdbcTypes(JdbcType.VARCHAR)
public class EncryptTypeHandler extends BaseTypeHandler<String> {

    @Override
    public void setNonNullParameter(PreparedStatement preparedStatement, int i, String parameter, JdbcType jdbcType) throws SQLException {

        // 为空校验
        if (StringUtils.isNotBlank(parameter)) {
            parameter = EncryptTool.encrypt(parameter);
        }
        preparedStatement.setString(i, parameter);
    }

    @Override
    public String getNullableResult(ResultSet resultSet, String columnName) throws SQLException {
        return getResult(resultSet.getString(columnName));
    }

    @Override
    public String getNullableResult(ResultSet resultSet, int columnIndex) throws SQLException {
        return getResult(resultSet.getString(columnIndex));
    }

    @Override
    public String getNullableResult(CallableStatement callableStatement, int columnIndex) throws SQLException {
        return getResult(callableStatement.getString(columnIndex));
    }

    /**
     * 返回数据
     *
     * @param value
     * @return
     */
    private String getResult(String value) {

        // 为空校验
        if (StringUtils.isNotBlank(value)) {
            return EncryptTool.decryptor(value);
        }
        return value;
    }

}
```
