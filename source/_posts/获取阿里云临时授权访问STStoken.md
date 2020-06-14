---
title: 获取阿里云临时授权访问STStoken
date: 2019-03-07 20:06:24
categories: 代码实现
tags: 阿里云
---

![](https://oss.forestyoung.top/获取STStoken.jpg)
<!--more-->

### 参考文档

[阿里云官方文档](https://www.alibabacloud.com/help/zh/doc-detail/27364.htm?spm=a2c63.p38356.b99.35.5c778d840l5Eaw)
[相关SDK](https://help.aliyun.com/document_detail/28788.html?spm=a2c4g.11186623.6.645.7f4a7905JfcrXA)

### RAM 和 STS 介绍

>阿里云权限管理机制包括访问控制（Resource Access Management，简称 RAM）和安全凭证管理（Security Token Service，简称 STS），可以根据需求使用不同权限的子账号来访问表格存储，也支持为用户提供访问的临时授权。使用RAM和STS能极大地提高管理的灵活性和安全性。
>
>RAM 的主要作用是控制账号系统的权限。通过使用 RAM 可以将在主账号的权限范围内创建子账号，给不同的子账号分配不同的权限，从而达到授权管理的目的。详情请参见[访问控制产品帮忙文档](https://www.alibabacloud.com/help/doc-detail/28627.htm)。
>
>STS 是一个安全凭证（Token）的管理系统，用来授予临时的访问权限，这样就可以通过 STS 来完成对于临时用户的访问授权。

### STS临时授权访问

>RAM子账号都是可以长期正常使用的，发生泄露后如果无法及时解除权限，会非常危险。
>
>当开发者的 app 被用户使用之后，用户的数据要写入 ram-test-dev 这个实例。当 app 的用户数据很多时，要求能够安全地授权给众多的 app 用户上传数据，并且保证多个用户之间存储的隔离。
>
>类似这种场景需要临时访问权限，应该使用 STS 来完成。STS 可以指定复杂的策略来对特定的用户进行限制，仅提供最小的权限。

### 使用

**1.创建和管理角色STS的AssumeRole接口的使用，需要在RAM中创建和管理角色**

**2.添加相关依赖**

```xml
<!--添加aliyun-java-sdk的相关依赖 start-->
<dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>aliyun-java-sdk-sts</artifactId>
    <version>3.0.0</version>
</dependency>
<dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>aliyun-java-sdk-core</artifactId>
    <version>3.5.0</version>
</dependency>
<!--添加aliyun-java-sdk的相关依赖 end-->
```

**3.配置文件中添加所需请求参数**

```properties
#阿里云访问控制RAM子账户信息,需要登录阿里云控制台查看(Secret只在创建时可查看,需要做好备份)
aliyun.oss.accessKeyId=#你的accessKeyId
aliyun.oss.accessKeySecret=#你的accessKeySecret
#角色ID，在角色中进行授权，获取相应的权限
aliyun.sts.roleArn=#你的roleArn
#指临时凭证的名称，一般来说建议使用不同的应用程序用户来区分。自定义(可不设置)
aliyun.sts.roleSessionName=test
```

**4.示例代码**

```java
/**
 * 获取Token返回结果
 * @author: young
 * @date: 2018年12月04日 13:56
 */
public class StsTokenVO implements Serializable {
    /**
     * 访问密钥标识
     */
    private String accessKeyId;
    /**
     * 访问密钥
     */
    private String accessKeySecret;
    /**
     * 安全令牌
     */
    private String securityToken;
}
```

```java
public class StsTokenServiceImpl implements StsTokenService {
    private static final Logger LOGGER = LoggerFactory.getLogger(StsTokenServiceImpl.class);
    @Value("${aliyun.oss.accessKeyId}")
    private String accessKeyId;
    @Value("${aliyun.oss.accessKeySecret}")
    private String accessKeySecret;
    @Value("${aliyun.sts.roleArn}")
    private String roleArn;
    @Value("${aliyun.sts.roleSessionName}")
    private String roleSessionName;
    /**
     * token失效时间，单位秒(不设置默认1小时,这里设置5分钟)
     */
    // private static final Long durationSeconds= 300L;
    private static final String ENDPOINT = "sts.aliyuncs.com";

    /**
     * 获取STStoken接口
     * @param:
     * @return: StsTokenVO
     * @author: young
     * @date: 2018/12/4 13:59
     */
    @Override
    public StsTokenVO getStsToken() {
        StsTokenVO tokenVO = new StsTokenVO();
        try {
            // 添加endpoint（直接使用STS endpoint，前两个参数留空，无需添加region ID）
            DefaultProfile.addEndpoint("", "", "Sts", ENDPOINT);
            // 构造default profile（参数留空，无需添加region ID）
            IClientProfile profile = DefaultProfile.getProfile("", accessKeyId, accessKeySecret);
            // 用profile构造client
            DefaultAcsClient client = new DefaultAcsClient(profile);
            final AssumeRoleRequest request = new AssumeRoleRequest();
            request.setMethod(MethodType.POST);
            request.setRoleArn(roleArn);
            request.setRoleSessionName(roleSessionName);
            // request.setDurationSeconds(durationSeconds);
            // 针对该临时权限可以根据该属性赋予规则，格式为json，没有特殊要求，默认为空
            // request.setPolicy(policy); // Optional
            final AssumeRoleResponse response = client.getAcsResponse(request);
            AssumeRoleResponse.Credentials credentials = response.getCredentials();
            tokenVO.setAccessKeyId(credentials.getAccessKeyId());
            tokenVO.setAccessKeySecret(credentials.getAccessKeySecret());
            tokenVO.setSecurityToken(credentials.getSecurityToken());
            return tokenVO;
        } catch (ClientException e) {
            LOGGER.error("获取阿里云STS临时授权权限失败，错误信息：" + e);
            return null;
        }
    }
}
```