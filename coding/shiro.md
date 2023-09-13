---
title: shiro
date: 2023/09/13
updated: 2023/09/13
categories:
  - coding
tags:
  - shiro
  - 编程基础
---
# 1、shiro基本概念

Shiro是Apache组织下的一个开源的安全框架，常常用来做：安全认证、权限管理、会话管理、加密

## 1.1、安全认证

可用于验证用户的身份凭证，如用户名和密码。通过配置 Shiro 的 Realm（领域）来自定义身份验证的逻辑，通过 Subject 对象进行身份验证，可以获取用户的身份信息和执行相关操作

## 1.2、权限管理

可以通过配置 Shiro 的 Realm 来定义用户的角色和权限信息，或者使用注解方式进行权限控制。通过 Subject 对象进行权限检查，可以判断用户是否具有某个角色或权限，并根据结果进行相应的处理

## 1.3、会话管理

shiro 提供了会话管理的功能，用于跟踪用户的登录状态和管理用户的会话信息。Shiro 的会话管理可以基于 Web 容器的 HttpSession，也可以使用自己的非 Web 环境会话管理（redis等）

## 1.4、加密

对密码进行加密和解密操作，以保护用户的密码安全。Shiro 支持常用的加密算法，如MD5、SHA、AES等。你可以根据需求选择适当的加密算法进行使用

# 2、shiro架构

![shiro](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202309131118959.jpg)

## 2.1、主体（Subject）

Subject即主体，外部应用与subject进行交互，subject记录了当前操作用户，将用户的概念理解为当前操作的主体，可能是一个通过浏览器请求的用户，也可能是一个运行的程序。Subject在shiro中是一个接口，接口中定义了很多认证授相关的方法，外部程序通过subject进行认证授，而subject是通过SecurityManager安全管理器进行认证授权


## 2.2、安全管理器（Security Manager）

SecurityManager即安全管理器，对全部的subject进行安全管理，它是shiro的核心，负责对所有的subject进行安全管理。通过SecurityManager可以完成subject的认证、授权等，实质上SecurityManager是通过Authenticator进行认证，通过Authorizer进行授权，通过SessionManager进行会话管理等。
SecurityManager是一个接口，继承了Authenticator, Authorizer, SessionManager这三个接口

## 2.3、认证器（Authenticator）

Authenticator即认证器，对用户身份进行认证，Authenticator是一个接口，shiro提供ModularRealmAuthenticator实现类，通过ModularRealmAuthenticator基本上可以满足大多数需求，也可以自定义认证器


## 2.4、授权器（Authorizer）

Authorizer即授权器，用户通过认证器认证通过，在访问功能时需要通过授权器判断用户是否有此功能的操作权限

## 2.5、领域（Realm）
Realm即领域，相当于datasource数据源，securityManager进行安全认证需要通过Realm获取用户权限数据，比如：如果用户身份数据在数据库那么realm就需要从数据库获取用户身份信息。

> 注意：不要把realm理解成只是从数据源取数据，在realm中还有认证授权校验的相关的代码。

## 2.6、会话管理器（SessionManager）

会话管理器负责管理主体的会话，跟踪用户的登录状态和管理会话数据。
在 Shiro 中，会话管理器可以基于 Web 容器的 HttpSession，也可以使用自己的非 Web 环境会话管理

# 3、认证

在shiro中,用户需要提供principlas(身份)和credentials(证明)给shiro，从而应用能验证用户身份

## 3.1、认证流程

1. 提交身份凭证：用户在应用程序的登录页面或其他身份验证入口提交身份和凭证，例如用户名和密码
2. 创建 Subject 对象：应用程序根据用户提交的身份凭证创建一个 Subject 对象，代表当前与应用程序交互的用户
3. 提交身份凭证给认证器：Subject 对象将身份凭证提交给 Shiro 的认证器（Authenticator）进行验证
4. 认证器验证身份凭证：认证器对身份凭证进行验证，通常是通过比对凭证与存储在数据源中的用户信息进行匹配。认证器可以使用一个或多个 Realm 来获取用户信息并进行验证。Realm 对获取到的用户信息与提交的身份凭证进行比对验证，判断凭证是否有效
5. 结果处理：如果身份验证成功，认证器将成功的身份信息存储在 Subject 对象中，以便后续使用

## 3.2、认证实现
### 3.2.1、自定义Realm
```java

/**
 * 自定义Realm
 */
public class CustomerRealm extends AuthorizingRealm {
    //授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        System.out.println("==================");
        return null;
    }

    //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        //在token中获取 用户名
        String principal = (String) token.getPrincipal();
        System.out.println(principal);

        //实际开发中应当 根据身份信息使用jdbc mybatis查询相关数据库
        //在这里只做简单的演示
        //假设username,password是从数据库获得的信息
        String username="zhangsan";
        String password="123456";
        if(username.equals(principal)){
            //参数1:返回数据库中正确的用户名
            //参数2:返回数据库中正确密码
            //参数3:提供当前realm的名字 this.getName();
            SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(principal,password,this.getName());
            return simpleAuthenticationInfo;
        }
        return null;
    }
}
```

### 3.2.2、用定义的Realm进行认证
```java
/**
 * 测试自定义的Realm
 */
public class TestAuthenticatorCusttomerRealm {

    public static void main(String[] args) {
        //1.创建安全管理对象 securityManager
        DefaultSecurityManager defaultSecurityManager = new DefaultSecurityManager();

        //2.给安全管理器设置realm（设置为自定义realm获取认证数据）
        defaultSecurityManager.setRealm(new CustomerRealm());
        //IniRealm realm = new IniRealm("classpath:shiro.ini");

        //3.给安装工具类中设置默认安全管理器
        SecurityUtils.setSecurityManager(defaultSecurityManager);

        //4.获取主体对象subject
        Subject subject = SecurityUtils.getSubject();

        //5.创建token令牌
        UsernamePasswordToken token = new UsernamePasswordToken("zhangsan", "123");
        try {
            subject.login(token);//用户登录
            System.out.println("登录成功~~");
        } catch (UnknownAccountException e) {
            e.printStackTrace();
            System.out.println("用户名错误!!");
        }catch (IncorrectCredentialsException e){
            e.printStackTrace();
            System.out.println("密码错误!!!");
        }
    }
}
```