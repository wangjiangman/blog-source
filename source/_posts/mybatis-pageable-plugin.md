title: "MyBatis 物理分页插件"
date: 2013-12-18
tags: Java
categories: 技术
---

我对分页组件有着比较简单原始的想法，就是传入一个分页参数对象，分页组件将分页对象返回。组件实现用到了Mybatis的拦截器，由于对Mybatis的原理并没有深入研究过，有些代码直接拷贝自网上，先按想法实现功能。
## 主要思路

1、分页对象采用spring-data中的Page和Pageable，其中Pageable作为分页请求，Page为返回的分页对象。
2、拦截器会对方法中的参数进行分析，如果发现Pageable类型的参数，则认为是分页请求，将请求封装为Page对象返回。

## 拦截器配置

``` xml 
<plugins>
    <plugin interceptor="org.buzheng.commons.mybatis.MybatisPageableInterceptor">
        <property name="dialectClass" value="org.buzheng.commons.mybatis.MySQLDialect"/>
    </plugin>
</plugins>
```

## Mapper接口方法写法

``` xml 
public interface SysUserDao {   
    Page<SysUser> findPageByParams(Map<String, Object> params, Pageable pageRequest);
}
```

## Mapper.xml 写法(和普通查询一样)

``` xml 
<select id="findPageByParams" resultType="SysUser">
    <include refid="findSql" />

    <where>
        <if test="param1.groupId != null">
            u.group_id = #{param1.groupId}
        </if>
    </where>

    ORDER BY u.add_time
</select>
```

## service 调用写法

``` java 
@Service
public class SysUserServiceImpl implements SysUserService {

    @Resource
    private SysUserDao sysUserDao;

    @Override
    public Page<SysUser> findPage(Map<String, Object> params, int pageNo, int pageSize) {

        Pageable pageRequest = new PageRequest(pageNo, pageSize);
        return this.sysUserDao.findPageByParams(params, pageRequest);
    }   
}
```
