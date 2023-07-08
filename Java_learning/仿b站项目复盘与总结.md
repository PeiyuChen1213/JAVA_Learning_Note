# 仿B站项目总结

## 第三章 后端经典开发模式

### 通过功能和与配置

1. 加解密工具类的使用

2. 统一封装的结果集 
```java
   public class JsonResponse<T> {
   
       private String code;
   
       private String msg;
   
       private T data;
   
       public JsonResponse(String code, String msg){
           this.code = code;
           this.msg = msg;
       }
   
       public JsonResponse(T data){
           this.data = data;
           msg = "成功";
           code = "0";
       }
   
       public static JsonResponse<String> success(){
           return new JsonResponse<>(null);
       }
   
       public static JsonResponse<String> success(String data){
           return new JsonResponse<>(data);
       }
   
       public static JsonResponse<String> fail(){
           return new JsonResponse<>("1", "失败");
       }
   
       public static JsonResponse<String> fail(String code, String msg){
           return new JsonResponse<>(code, msg);
       }
   
       public String getCode() {
           return code;
       }
   
       public void setCode(String code) {
           this.code = code;
       
   
       public String getMsg() {
           return msg;
       }
   
       public void setMsg(String msg) {
           this.msg = msg;
       }
   
       public T getData() {
           return data;
       }
   
       public void setData(T data) {
           this.data = data;
       }
   }
```

3. Json信息转换配置类 （让返回的Json数据更加规范）

   ```java
   @Configuration
   public class JsonHttpMessageConverterConfig {
       @Bean
       @Primary
       public HttpMessageConverters fastJsonHttpMessageConverters(){
           FastJsonHttpMessageConverter fastConverter = new FastJsonHttpMessageConverter();
           FastJsonConfig fastJsonConfig = new FastJsonConfig();
           fastJsonConfig.setDateFormat("yyyy-MM-dd HH:mm:ss");
           fastJsonConfig.setSerializerFeatures(
                   SerializerFeature.PrettyFormat,
                   SerializerFeature.WriteNullStringAsEmpty,
                   SerializerFeature.WriteNullListAsEmpty,
                   SerializerFeature.WriteMapNullValue,
                   SerializerFeature.MapSortField,
                   SerializerFeature.DisableCircularReferenceDetect
           );
           fastConverter.setFastJsonConfig(fastJsonConfig);
           //如果使用feign进行微服务间的接口调用，则需要加上该配置
           fastConverter.setSupportedMediaTypes(Collections.singletonList(MediaType.APPLICATION_JSON));
           return new HttpMessageConverters(fastConverter);
       }
   }
   ```

4. 全局异常处理的配置

   异常处理器

   ```java
   
   @ControllerAdvice
   @Order(Ordered.HIGHEST_PRECEDENCE)
   public class CommonGlobalExceptionHandler {
   
       @ExceptionHandler(value = Exception.class)
       @ResponseBody
       public JsonResponse<String> commonExceptionHandler(HttpServletRequest request, Exception e){
           String errorMsg = e.getMessage();
   //        如果异常的类型是我们自定义的异常，将异常的状态码和异常信息返回给前端用于做其他处理
           if(e instanceof ConditionException){
               String errorCode = ((ConditionException)e).getCode();
               return new JsonResponse<>(errorCode, errorMsg);
           }else{
               return new JsonResponse<>("500",errorMsg);
           }
       }
   }
   ```

   自定义的异常类

   ```java
   public class ConditionException extends RuntimeException{
   
       private static final long serialVersionUID = 1L;
   
       private String code;
       public ConditionException(String code, String name){
           super(name);
           this.code = code;
       }
       public ConditionException(String name){
           super(name);
           code = "500";
       }
   
       public String getCode() {
           return code;
       }
   
       public void setCode(String code) {
           this.code = code;
       }
   }
   ```

### 用户注册与登录模块的开发

在这个阶段主要开发的接口是：用于注册，用户登录，获取公钥

#### 数据表的设计

1. 用户表

   ```mysql
   CREATE TABLE `t_user` (
     `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键',
     `phone` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '手机号',
     `email` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '邮箱',
     `password` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '密码',
     `salt` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '盐值',
     `createTime` datetime DEFAULT NULL COMMENT '创建时间',
     `updateTime` datetime DEFAULT NULL COMMENT '更新时间',
     PRIMARY KEY (`id`)
   ) ENGINE=InnoDB AUTO_INCREMENT=1019 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='用户表';
   ```

2. 用户信息表

   ```mysql
   CREATE TABLE `t_user_info` (
     `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键',
     `userId` bigint DEFAULT NULL COMMENT '用户id',
     `nick` varchar(100) DEFAULT NULL COMMENT '昵称',
     `avatar` varchar(255) DEFAULT NULL COMMENT '头像',
     `sign` text COMMENT '签名',
     `gender` varchar(2) DEFAULT NULL COMMENT '性别：0男 1女 2未知',
     `birth` varchar(20) DEFAULT NULL COMMENT '生日',
     `createTime` datetime DEFAULT NULL COMMENT '创建时间',
     `updateTime` datetime DEFAULT NULL COMMENT '更新时间',
     PRIMARY KEY (`id`)
   ) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='用户基本信息表';
   ```
   
   用户表和用户信息表之间是一对一的关系

#### 接口开发

1. 创建user表对应的实体类

2. 创建userInfo表对应的实体类

3. 创建userService，UserApi，UserDao的类或者接口（为了简介期间，dao层的sql语句不记录了）

4. 开发UserApi当中的接口方法

   - ```java
     @RestController
     public class UserController {
     
         @Autowired
         private UserService userService;
     
         
         @GetMapping("/users")
         public JsonResponse<User> getUserInfo(){
             Long userId = userSupport.getCurrentUserId();
             User user = userService.getUserInfo(userId);
             return new JsonResponse<>(user);
         }
         
     	// 获取RSA公钥的接口配合使用
         @GetMapping("/rsa-pks")
         public JsonResponse<String> getRsaPulicKey(){
             String pk = RSAUtil.getPublicKeyStr();
             return new JsonResponse<>(pk);
         }
     
         // 新增用户对象 用户注册
         @PostMapping("/users")
         public JsonResponse<String> addUser(@RequestBody User user){
             userService.addUser(user);
             return JsonResponse.success();
         }
     }
     
     ```

     对应service的方法

     ```java
      public void addUser(User user) {
             String phone = user.getPhone();
             if(StringUtils.isNullOrEmpty(phone)){
                 throw new ConditionException("手机号不能为空！");
             }
             User dbUser = this.getUserByPhone(phone);
             if(dbUser != null){
                 throw new ConditionException("该手机号已经注册！");
             }
             Date now = new Date();
             String salt = String.valueOf(now.getTime());
             String password = user.getPassword();
             String rawPassword;
             try{
                 rawPassword = RSAUtil.decrypt(password);
             }catch (Exception e){
                 throw new ConditionException("密码解密失败！");
             }
             String md5Password = MD5Util.sign(rawPassword, salt, "UTF-8");
             user.setSalt(salt);
             user.setPassword(md5Password);
             user.setCreateTime(now);
             userDao.addUser(user);
         }
     ```

#### 基于JWT的用户Token的验证

**验证流程**

服务端验证浏览器携带的用户名和密码，验证通过后生成用户令牌（token）并返回给浏览器，浏览器再次访问时携带token，服务端校验token并返回相关数据

**优点**：

token不储存在服务器，不会造成服务器压力；token可以存储在非cookie中，安全性高；分布式系统下扩展性强

JWT：全称是JSON Web Token，JWT是一个规范，用于在空间受限环境下安全传递“声明”。

JWT的组成：JWT分成三部分，第一部分是头部（header），第二部分是载荷（payload），第三部分是签名（signature）

JWT优点：跨语言支持、便于传输、易于扩展

JWT头部：声明的类型、声明的加密算法（通常使用SHA256）

JWT载荷：存放有效信息，一般包含签发者、所面向的用户、接受方、过期时间、签发时间以及唯一身份标识

JWT签名：主要由头部、载荷以及秘钥组合加密而成

**Token 相关的工具类**

```java
public class TokenUtil {

    private static final String ISSUER = "签发者";

    public static String generateToken(Long userId) throws Exception{
        Algorithm algorithm = Algorithm.RSA256(RSAUtil.getPublicKey(), RSAUtil.getPrivateKey());
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(new Date());
        calendar.add(Calendar.HOUR, 1);
        return JWT.create().withKeyId(String.valueOf(userId))
                .withIssuer(ISSUER)
                .withExpiresAt(calendar.getTime())
                .sign(algorithm);
    }

    public static String generateRefreshToken(Long userId) throws Exception{
        Algorithm algorithm = Algorithm.RSA256(RSAUtil.getPublicKey(), RSAUtil.getPrivateKey());
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(new Date());
        calendar.add(Calendar.DAY_OF_MONTH, 7);
        return JWT.create().withKeyId(String.valueOf(userId))
                .withIssuer(ISSUER)
                .withExpiresAt(calendar.getTime())
                .sign(algorithm);
    }

    public static Long verifyToken(String token){
        try{
            Algorithm algorithm = Algorithm.RSA256(RSAUtil.getPublicKey(), RSAUtil.getPrivateKey());
            JWTVerifier verifier = JWT.require(algorithm).build();
            DecodedJWT jwt = verifier.verify(token);
            String userId = jwt.getKeyId();
            return Long.valueOf(userId);
        }catch (TokenExpiredException e){
            throw new ConditionException("555","token过期！");
        }catch (Exception e){
            throw new ConditionException("非法用户token！");
        }
    }
```

一个可以获取当前登录用户的用户id的方法

```java
public Long getCurrentUserId() {
        //获取请求相关信息的类
        ServletRequestAttributes requestAttributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = requestAttributes.getRequest();
//       从请求头当中获取当前的token
        String token = request.getHeader("token");
        Long userId = TokenUtil.verifyToken(token);
//       userId一般都是大于0的
        if(userId < 0) {
            throw new ConditionException("非法用户");
        }
//        this.verifyRefreshToken(userId);
        return userId;
    }
```

上面获取到当前登录的id之后就可以得到当前的用户信息了

对应的接口方法如下：

```java
    @GetMapping("/users")
    public JsonResponse<User> getUserInfo(){
        Long userId = userSupport.getCurrentUserId();
        User user = userService.getUserInfo(userId);
        return new JsonResponse<>(user);
    }

	//service 层
	   public User getUserInfo(Long userId) {
        User user = userDao.getUserById(userId);
        UserInfo userInfo = 	userDao.getUserInfoByUserId(userId);
        user.setUserInfo(userInfo);
        return user;
    }
```

**做到这里，梳理一下基于jwt的登录流程**：

1. 首先，登录的时候，前端将输入的userId和password传给服务端 （login接口）
2. login 接口接收到参数之后，校验账号密码是否正确，正确之后将生成一个token返回给前端
3. 前端可以将这个token存储在浏览器的localStorage当中
4. 之后每次发送请求都将这个token放到请求头当中一同发送

### 用户关注的开发

数据库表：用户关注表，用户关注分组表

API接口:  关注用户、关注列表、粉丝列表、分页查询用户 

#### 数据库的表设计

用户关注表 

```mysql
CREATE TABLE `t_user_following` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `userId` bigint DEFAULT NULL COMMENT '用户id',
  `followingId` int DEFAULT NULL COMMENT '关注用户id',
  `groupId` int DEFAULT NULL COMMENT '关注分组id',
  `createTime` datetime DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=26 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='用户关注表';
```

用户关注分组表

```mysql
CREATE TABLE `t_following_group` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `userId` bigint DEFAULT NULL COMMENT '用户id',
  `name` varchar(50) DEFAULT NULL COMMENT '关注分组名称',
  `type` varchar(5) DEFAULT NULL COMMENT '关注分组类型：0特别关注  1悄悄关注 2默认分组  3用户自定义分组',
  `createTime` datetime DEFAULT NULL COMMENT '创建时间',
  `updateTime` datetime DEFAULT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=16 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='用户关注分组表';
```

#### 添加用户关注

1. 根据上述的 表创建对应的实体类

2. 创建对应的service层和dao层 

3. 创建userFollowingApi接口

   ```java
   
       /**
        * 新增用户关注的api接口
        * @param userFollowing
        * @return
        */
       @PostMapping("/user-followings")
       public JsonResponse<String> addUserFollowings(@RequestBody UserFollowing userFollowing){
           Long userId = userSupport.getCurrentUserId();
           userFollowing.setUserId(userId);
           userFollowingService.addUserFollowings(userFollowing);
           return JsonResponse.success();
       }
   ```

   service 层对应的方法

   ```java
    @Transactional
       public void addUserFollowings(UserFollowing userFollowing) {
           // 判断前端是否将分组的id传入
           Long groupId = userFollowing.getGroupId();
           if(groupId == null){
               //如果传入的分组id为空 说明没传入 放到默认分组当中
               FollowingGroup followingGroup = followingGroupService.getByType(UserConstant.USER_FOLLOWING_GROUP_TYPE_DEFAULT);
               userFollowing.setGroupId(followingGroup.getId());
           }else{
               FollowingGroup followingGroup = followingGroupService.getById(groupId);
               if(followingGroup == null){
                   throw new ConditionException("关注分组不存在！");
               }
           }
           //获取关注的用户
           Long followingId = userFollowing.getFollowingId();
           User user = userService.getUserById(followingId);
           if(user == null){
               throw new ConditionException("关注的用户不存在！");
           }
   //      如果有记录，先删除掉对应的记录 然后再重新添加，以达到更新的作用
           userFollowingDao.deleteUserFollowing(userFollowing.getUserId(), followingId);
           userFollowing.setCreateTime(new Date());
           userFollowingDao.addUserFollowing(userFollowing);
       }
   ```



#### 获取关注列表

api接口

```java
    /**
     * 获取用户关注列表
     * @return
     */
    @GetMapping("/user-followings")
    public JsonResponse<List<FollowingGroup>> getUserFollowings(){
        Long userId = userSupport.getCurrentUserId();
        List<FollowingGroup> result = userFollowingService.getUserFollowings(userId);
        return new JsonResponse<>(result);
    }
```

service层

```java
  	// 第一步：获取关注的用户列表
    // 第二步：根据关注用户的id查询关注用户的基本信息
    // 第三步：将关注用户按关注分组进行分类
    public List<FollowingGroup> getUserFollowings(Long userId){
//        根据id获取对应的关注列表
        List<UserFollowing> list = userFollowingDao.getUserFollowings(userId);
//        将关注列表当中的关注id抽取出来 形成一个关注id的set
        Set<Long> followingIdSet = list.stream().map(UserFollowing::getFollowingId).collect(Collectors.toSet());
        List<UserInfo> userInfoList = new ArrayList<>();
        if(followingIdSet.size() > 0){
//            根据关注id获取关注人的信息
            userInfoList = userService.getUserInfoByUserIds(followingIdSet);
        }
//       找到每个用户关注对象对应的关注人的用户信息并匹配
        for(UserFollowing userFollowing : list){
            for(UserInfo userInfo : userInfoList){
                if(userFollowing.getFollowingId().equals(userInfo.getUserId())){
                    userFollowing.setUserInfo(userInfo);
                }
            }
        }
//        获取和该用户相关的组的列表
        List<FollowingGroup> groupList = followingGroupService.getByUserId(userId);
//        全部关注，不需要存在数据库当中但是前端需要
        FollowingGroup allGroup = new FollowingGroup();
        allGroup.setName(UserConstant.USER_FOLLOWING_GROUP_ALL_NAME);
        allGroup.setFollowingUserInfoList(userInfoList);
        List<FollowingGroup> result = new ArrayList<>();
        result.add(allGroup);
//        获取全部的关注分组
        for(FollowingGroup group : groupList){
            List<UserInfo> infoList = new ArrayList<>();
            for(UserFollowing userFollowing : list){
//              关注分组的一个匹配
                if(group.getId().equals(userFollowing.getGroupId())){
                    infoList.add(userFollowing.getUserInfo());
                }

            }
//           将属于该组的所有的用户信息写到这个组当中去
            group.setFollowingUserInfoList(infoList);

            result.add(group);
        }
        return result;
    }
```

上述的代码逻辑大致可以简述为如下步骤：

1. 首先，根据传入的userI去userFollowing表中查询对应关注的userFollowing对象
2. 从uesrFollowingId当中去userInfo表当中查到对应的用户信息 
3. 将用户信息和用户关注对象做一个绑定
4. 查询所有分组，每个分组绑定各自的用户信息
5. 返回对应的 FollowingGroup集合

#### 获取用户粉丝列表

api接口

```java
    /**
     * 获取用户粉丝列表的接口
     * @return
     */
    @GetMapping("/user-fans")
    public JsonResponse<List<UserFollowing>> getUserFans(){
        Long userId = userSupport.getCurrentUserId();
        List<UserFollowing> result = userFollowingService.getUserFans(userId);
        return new JsonResponse<>(result);
    }
```

service层

```java
 // 第一步：获取当前用户的粉丝列表
    // 第二步：根据粉丝的用户id查询基本信息
    // 第三步：查询当前用户是否已经关注该粉丝
    public List<UserFollowing> getUserFans(Long userId){
        List<UserFollowing> fanList = userFollowingDao.getUserFans(userId);
        Set<Long> fanIdSet = fanList.stream().map(UserFollowing::getUserId).collect(Collectors.toSet());
        List<UserInfo> userInfoList = new ArrayList<>();
        if(fanIdSet.size() > 0){
            userInfoList = userService.getUserInfoByUserIds(fanIdSet);
        }
        List<UserFollowing> followingList = userFollowingDao.getUserFollowings(userId);
        for(UserFollowing fan : fanList){
            for(UserInfo userInfo : userInfoList){
                if(fan.getUserId().equals(userInfo.getUserId())){
                    userInfo.setFollowed(false);
                    fan.setUserInfo(userInfo);
                }
            }
            for(UserFollowing following : followingList){
                if(following.getFollowingId().equals(fan.getUserId())){
                    fan.getUserInfo().setFollowed(true);
                }
            }
        }
        return fanList;
    }
```

#### 新建用户关注分组

api接口如下

```java
  /**
     * 添加关注分组
     * @param followingGroup
     * @return
     */
    @PostMapping("/user-following-groups")
    public JsonResponse<Long> addUserFollowingGroups(@RequestBody FollowingGroup followingGroup){
        Long userId = userSupport.getCurrentUserId();
        followingGroup.setUserId(userId);
        Long groupId = userFollowingService.addUserFollowingGroups(followingGroup);
        return new JsonResponse<>(groupId);
    }
```

#### 获取用户关注分组

直接根据当前登录用户的用户id去数据库当中查询

```java
    /**
     * 获取关注分组
     *
     * @return
     */
    @GetMapping("/user-following-groups")
    public JsonResponse<List<FollowingGroup>> getUserFollowingGroups() {
        Long userId = userSupport.getCurrentUserId();
        List<FollowingGroup> list = userFollowingService.getUserFollowingGroups(userId);
        return new JsonResponse<>(list);
    }

```

#### 分页查询用户信息

api接口层

```java
    /**
     *
     * @param no 当前页
     * @param size 每页的记录数
     * @param nick 昵称
     * @return
     */
    @GetMapping("/user-infos")
    public JsonResponse<PageResult<UserInfo>> pageListUserInfos(@RequestParam Integer no, @RequestParam Integer size, String nick){
        Long userId = userSupport.getCurrentUserId();
//        封装一个传入的参数对象
        JSONObject params = new JSONObject();
        params.put("no", no);
        params.put("size", size);
        params.put("nick", nick);
        params.put("userId", userId);
        PageResult<UserInfo> result = userService.pageListUserInfos(params);
//        如果查出的数据是大于0 的
        if(result.getTotal() > 0){
//            判断关注状态 确认是否有关注
            List<UserInfo> checkedUserInfoList = userFollowingService.checkFollowingStatus(result.getList(), userId);
            result.setList(checkedUserInfoList);
        }
        return new JsonResponse<>(result);
    }
```

service层

```java
    public PageResult<UserInfo> pageListUserInfos(JSONObject params) {
//        计算limit的其实参数和size参数
        Integer no = params.getInteger("no");
        Integer size = params.getInteger("size");
        params.put("start", (no-1)*size);
        params.put("limit", size);
        Integer total = userDao.pageCountUserInfos(params);
        List<UserInfo> list = new ArrayList<>();
        if(total > 0){
            list = userDao.pageListUserInfos(params);
        }
        return new PageResult<>(total, list);
    }
```

checkFollowingStatus 方法

```java
    /**
     * 判断关注的情况
     * @param userInfoList 分页查询的用户信息表
     * @param userId 用户id
     * @return
     */
    public List<UserInfo> checkFollowingStatus(List<UserInfo> userInfoList, Long userId) {
//        获取用户当前关注的用户
        List<UserFollowing> userFollowingList = userFollowingDao.getUserFollowings(userId);
        for(UserInfo userInfo : userInfoList){
            userInfo.setFollowed(false);
            for(UserFollowing userFollowing : userFollowingList){
//                如果关注用户对象的id和当前的id相同 说明说已经关注了
                if(userFollowing.getFollowingId().equals(userInfo.getUserId())){
                    userInfo.setFollowed(true);
                }
            }
        }
        return userInfoList;
    }
```

### 动态提醒的开发

主要功能是用户发布动态，粉丝可以收到发布的动态

数据库表：用户动态表 t_user_moments

设计模式：订阅发布模式

相关的接口：用户发布动态，用户查询订阅内容的动态

#### 数据库表设计

```java
CREATE TABLE `t_user_moments` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `userId` bigint DEFAULT NULL COMMENT '用户id',
  `type` varchar(5) DEFAULT NULL COMMENT '动态类型：0视频 1直播 2专栏动态',
  `contentId` bigint DEFAULT NULL COMMENT '内容详情id',
  `createTime` datetime DEFAULT NULL COMMENT '创建时间',
  `updateTime` datetime DEFAULT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='用户动态表';
```

#### 订阅发布模式

1. 发布者将消息推送给代理人
2. 代理人告诉所有的订阅者
3. 订阅者来拉取消息获取信息

#### 动态题型的实现方式

1. RocketMQ：纯java编写的开源消息中间件，特点是：高性能、低延迟、分布式事务
2. Redis：高性能缓存工具，数据存储在内存中，读写速度非常快

#### MQ的配置类和工具类实现

配置类

```java
package com.imooc.bilibili.service.config;

import com.alibaba.fastjson.JSONArray;
import com.alibaba.fastjson.JSONObject;
import com.imooc.bilibili.domain.UserFollowing;
import com.imooc.bilibili.domain.UserMoment;
import com.imooc.bilibili.domain.constant.UserMomentsConstant;
import com.imooc.bilibili.service.UserFollowingService;
import com.imooc.bilibili.service.websocket.WebSocketService;
import io.netty.util.internal.StringUtil;
import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;
import org.apache.rocketmq.client.producer.DefaultMQProducer;
import org.apache.rocketmq.common.message.MessageExt;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.core.RedisTemplate;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

@Configuration
public class RocketMQConfig {

    @Value("${rocketmq.name.server.address}")
    private String nameServerAddr;

    @Autowired
    private RedisTemplate<String, String> redisTemplate;

    @Autowired
    private UserFollowingService userFollowingService;

    @Bean("momentsProducer")
    public DefaultMQProducer momentsProducer() throws Exception{
        DefaultMQProducer producer = new DefaultMQProducer(UserMomentsConstant.GROUP_MOMENTS);
        producer.setNamesrvAddr(nameServerAddr);
        producer.start();
        return producer;
    }

      @Bean("momentsConsumer")
    public DefaultMQPushConsumer momentsConsumer() throws Exception {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer(UserMomentsConstant.GROUP_MOMENTS);
        consumer.setNamesrvAddr(nameServerAddr);
        consumer.subscribe(UserMomentsConstant.TOPIC_MOMENTS, "*");
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {
//                由于我们发送动态的时候只发一条数据，所以监听到的也只有一条
                MessageExt msg = msgs.get(0);
                if (msg == null) {
                    return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
                }
//              拿到msg的内容
                String bodyStr = new String(msg.getBody());
//              将拿到的json数据转成java对象
                UserMoment userMoment = JSONObject.toJavaObject(JSONObject.parseObject(bodyStr), UserMoment.class);
                Long userId = userMoment.getUserId();
//              获得粉丝的列表
                List<UserFollowing> fanList = userFollowingService.getUserFans(userId);
                for (UserFollowing fan : fanList) {
//                  取到粉丝的id 拼接成一个key
                    String key = "subscribed-" + fan.getUserId();
//                    获取到当前粉丝的动态推送列表 因为不止一个用户不止一个关注
                    String subscribedListStr = redisTemplate.opsForValue().get(key);
                    List<UserMoment> subscribedList;
                    if (StringUtil.isNullOrEmpty(subscribedListStr)) {
                        subscribedList = new ArrayList<>();
                    } else {
//                       将字符串转成列表
                        subscribedList = JSONArray.parseArray(subscribedListStr, UserMoment.class);
                    }
//                    添加动态
                    subscribedList.add(userMoment);
//                    将粉丝的动态列表重新set到redis数据库
                    redisTemplate.opsForValue().set(key, JSONObject.toJSONString(subscribedList));
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        consumer.start();
        return consumer;
    }
```

MQ的工具类的实现

```java
public class RocketMQUtil {

    /**
     * 同步发送消息
     * @param producer 生产者
     * @param msg 消息
     * @throws Exception
     */
    public static void syncSendMsg(DefaultMQProducer producer, Message msg) throws Exception{
        SendResult result = producer.send(msg);
        System.out.println(result);
    }


    /**
     * 异步发送消息
     * @param producer 生产者
     * @param msg 消息
     * @throws Exception
     */
    public static void asyncSendMsg(DefaultMQProducer producer, Message msg) throws Exception{
        producer.send(msg, new SendCallback() {
            @Override
            public void onSuccess(SendResult sendResult) {
                Logger logger = LoggerFactory.getLogger(RocketMQUtil.class);
                logger.info("异步发送消息成功，消息id：" + sendResult.getMsgId());
            }
            @Override
            public void onException(Throwable e) {
                e.printStackTrace();
            }
        });
    }
}
```

#### 新增用户动态

Api接口方法

```java
@Autowired
private UserMomentsService userMomentsService;

@Autowired
private UserSupport userSupport;

@PostMapping("/user-moments")
public JsonResponse<String> addUserMoments(@RequestBody UserMoment userMoment) throws Exception {
    Long userId = userSupport.getCurrentUserId();
    userMoment.setUserId(userId);
    userMomentsService.addUserMoments(userMoment);
    return JsonResponse.success();
}
```

Service

```java
    /**
     * 添加用户动态
     * @param userMoment
     * @throws Exception
     */
    public void addUserMoments(UserMoment userMoment) throws Exception {
        userMoment.setCreateTime(new Date());
        userMomentsDao.addUserMoments(userMoment);
        DefaultMQProducer producer = (DefaultMQProducer)applicationContext.getBean("momentsProducer");
//        封装消息，将对象转成json字符串 然后获取字符串的byte数组
        Message msg = new Message(UserMomentsConstant.TOPIC_MOMENTS, JSONObject.toJSONString(userMoment).getBytes(StandardCharsets.UTF_8));
        RocketMQUtil.syncSendMsg(producer, msg);
    }
```

#### 消费用户动态

主要逻辑在配置文件当中的消费者模块当中

```java
 @Bean("momentsConsumer")
    public DefaultMQPushConsumer momentsConsumer() throws Exception {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer(UserMomentsConstant.GROUP_MOMENTS);
        consumer.setNamesrvAddr(nameServerAddr);
        consumer.subscribe(UserMomentsConstant.TOPIC_MOMENTS, "*");
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {
//                由于我们发送动态的时候只发一条数据，所以监听到的也只有一条
                MessageExt msg = msgs.get(0);
                if (msg == null) {
                    return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
                }
//              拿到msg的内容
                String bodyStr = new String(msg.getBody());
//              将拿到的json数据转成java对象
                UserMoment userMoment = JSONObject.toJavaObject(JSONObject.parseObject(bodyStr), UserMoment.class);
                Long userId = userMoment.getUserId();
//              获得粉丝的列表
                List<UserFollowing> fanList = userFollowingService.getUserFans(userId);
                for (UserFollowing fan : fanList) {
//                  取到粉丝的id 拼接成一个key
                    String key = "subscribed-" + fan.getUserId();
//                  获取到当前粉丝的动态推送列表 因为一个用户不止一个关注
                    String subscribedListStr = redisTemplate.opsForValue().get(key);
                    List<UserMoment> subscribedList;
                    if (StringUtil.isNullOrEmpty(subscribedListStr)) {
                        subscribedList = new ArrayList<>();
                    } else {
//                       将字符串转成列表
                        subscribedList = JSONArray.parseArray(subscribedListStr, UserMoment.class);
                    }
//                    添加动态
                    subscribedList.add(userMoment);
//                    将粉丝的动态列表重新set到redis数据库
                    redisTemplate.opsForValue().set(key, JSONObject.toJSONString(subscribedList));
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        consumer.start();
        return consumer;
    }
```

主要逻辑：

1. 当消费者监听到生成者发送的动态之后 取出当时的动态
2. 获取当前用户的用户粉丝 
3. 以粉丝的userId为key 取出原来存在redis当中的消息的list然后加上
4. 将新的动态列表转成json字符串又加入到redis当中 
5. 序列化的是用户所有的关注的动态列表也就是subscribedList （因为往往一个用户有多个关注对象，所以推送的动态肯定有可能是多条的）

#### 查询用户动态

直接在redis数据库当中根据键来查询对应的列表

service

```java
    /**
     * 获取动态
     * @return 动态的列表
     */
    @GetMapping("/user-subscribed-moments")
    public JsonResponse<List<UserMoment>> getUserSubscribedMoments(){
        Long userId = userSupport.getCurrentUserId();
        List<UserMoment> list = userMomentsService.getUserSubscribedMoments(userId);
        return new JsonResponse<>(list);
    }
```

getUserSubscribedMoments()方法

```java
    /**
     * 获取订阅的用户动态
     * @param userId
     * @return
     */
    public List<UserMoment> getUserSubscribedMoments(Long userId) {
        String key = "subscribed-" + userId;
        String listStr = redisTemplate.opsForValue().get(key);
        return JSONArray.parseArray(listStr, UserMoment.class);
    }
```

### RBAC(ROLE BASED ACCESS CONTROL) 权限控制总结

#### 数据库表设计

角色表、用户角色关联表、元素操作权限表、角色元素操作权限关联表、页面菜单权限表、角色页面菜单权限关联表

![image-20230225211213361](https://raw.githubusercontent.com/PeiyuChen1213/JAVA_Learning_Note/master/img/image-20230225211213361.png)

#### 开发准备

1. 创建对应的实体类：UserAuthorities，UserRole，AuthRoleMenu，AuthRoleElementOperation，AuthRole，AuthMenu，AuthElementOperation（见代码）
2. 创建api和service接口
3. 连表查询编写sql

#### 操作权限和菜单权限的开发

获取当前用户的操作和菜单权限的API接口（前端用）

```java
    /**
     * 获取当前的权限
     * @return
     */
    @GetMapping("/user-authorities")
    public JsonResponse<UserAuthorities> getUserAuthorities(){
//        获取当前的用户id
        Long userId = userSupport.getCurrentUserId();
        UserAuthorities userAuthorities = userAuthService.getUserAuthorities(userId);
        return new JsonResponse<>(userAuthorities);
    }
```

service 层

```java
    public UserAuthorities getUserAuthorities(Long userId) {
//      连表查询 获取用户的角色表
        List<UserRole> userRoleList = userRoleService.getUserRoleByUserId(userId);
//        将角色表的id拿到
        Set<Long> roleIdSet = userRoleList.stream().map(UserRole :: getRoleId).collect(Collectors.toSet());
//        获取当前的 AuthRoleElementOperation表 根据角色id去查当前角色的操作权限
        List<AuthRoleElementOperation> roleElementOperationList = authRoleService.getRoleElementOperationsByRoleIds(roleIdSet);
//        根据角色id去查当前角色的菜单权限
        List<AuthRoleMenu> authRoleMenuList = authRoleService.getAuthRoleMenusByRoleIds(roleIdSet);
//        将所有的权限封装在一个userAuthorities
        UserAuthorities userAuthorities = new UserAuthorities();
        userAuthorities.setRoleElementOperationList(roleElementOperationList);
        userAuthorities.setRoleMenuList(authRoleMenuList);
        return userAuthorities;
    }

```

主要思路：

1. 这是一个比较复杂的连表查询 收根据当前的id可以去数据库查当前角色列表
2. 得到列表之后我们就可以得到角色的id
3. 有了角色的id就可以去中间表当中获取权限
4. 再将两种权限set到一个权限类当中去

#### 后端接口和数据权限控制（Spring AOP实现）

**API接口权限控制**

```java
@Order(1)
@Component
@Aspect
public class ApiLimitedRoleAspect {

    @Autowired
    private UserSupport userSupport;

    @Autowired
    private UserRoleService userRoleService;

    @Pointcut("@annotation(com.imooc.bilibili.domain.annotation.ApiLimitedRole)")
    public void check(){
    }

    @Before("check()&& @annotation(apiLimitedRole)")
    public void doBefore(JoinPoint joinPoint, ApiLimitedRole apiLimitedRole){
        //获取当前得userId
        Long userId = userSupport.getCurrentUserId();
        //根据UserId查看当前用户有哪些角色
        List<UserRole> userRoleList = userRoleService.getUserRoleByUserId(userId);
        String[] limitedRoleCodeList = apiLimitedRole.limitedRoleCodeList();
//        接口需要的权限角色
        Set<String> limitedRoleCodeSet = Arrays.stream(limitedRoleCodeList).collect(Collectors.toSet());
//        当前用户的权限角色
        Set<String> roleCodeSet = userRoleList.stream().map(UserRole::getRoleCode).collect(Collectors.toSet());
//        取其中的交集
        roleCodeSet.retainAll(limitedRoleCodeSet);
        if(roleCodeSet.size() > 0){
            throw new ConditionException("权限不足！");
        }
    }
}
```

主要的思路：

1. 获取当前用户的角色
2. 获取注解上传入的受限制的角色
3. 将受限制的角色code和当前用户的角色id进行比较，如果存在交集则说明权限不足(retainAll方法)

**数据的权限控制**

同样也是使用springAOP的方法来实现数据的权限控制

```java
@Order(1)
@Component
@Aspect
public class DataLimitedAspect {

    @Autowired
    private UserSupport userSupport;

    @Autowired
    private UserRoleService userRoleService;

    @Pointcut("@annotation(com.imooc.bilibili.domain.annotation.DataLimited)")
    public void check(){
    }

    /**
     * 限制等级低的用户发布动态类型
     * @param joinPoint
     */
    @Before("check()")
    public void doBefore(JoinPoint joinPoint){
        Long userId = userSupport.getCurrentUserId();
//        获取当前的角色
        List<UserRole> userRoleList = userRoleService.getUserRoleByUserId(userId);
//        获取当前的角色code
        Set<String> roleCodeSet = userRoleList.stream().map(UserRole::getRoleCode).collect(Collectors.toSet());
//        获取切入点方法的参数 -- public JsonResponse<String> addUserMoments(@RequestBody UserMoment userMoment) 也就是UserMoment参数
        Object[] args = joinPoint.getArgs();
        for(Object arg : args){
          if(arg instanceof UserMoment){
              UserMoment userMoment = (UserMoment)arg;
              String type = userMoment.getType();
//              等级低的用户不能发type==0的动态
              if(roleCodeSet.contains(AuthRoleConstant.ROLE_LV1) && !"0".equals(type)){
                  throw new ConditionException("参数异常");
              }
          }
        }
    }
}
```

主要思路：

1. 通过AOP得到连接点的方法的参数
2. 获取UserMoment参数当中的type字段进行校验（如上代码所示）

### 添加用户默认的等级

有了等级角色关系的时候，在注册用户的时候就应该需要一个给用户一个默认的角色

在userAuthService当中创建一个添加用户权限的方法

```java
    public void addUserDefaultRole(Long id) {
        UserRole userRole = new UserRole();
        AuthRole role = authRoleService.getRoleByCode(AuthRoleConstant.ROLE_LV0);
        userRole.setUserId(id);
        userRole.setRoleId(role.getId());
        userRoleService.addUserRole(userRole);
    }
```

然后在注册的时候调用这个方法

```java
public void addUser(User user) {
        String phone = user.getPhone();
        if (StringUtils.isNullOrEmpty(phone)) {
            throw new ConditionException("手机号不能为空！");
        }
        //查询数据库判断该用户是否已经在数据库当中了
        User dbUser = this.getUserByPhone(phone);
        if (dbUser != null) {
            throw new ConditionException("该手机号已经注册！");
        }
        Date now = new Date();
        //根据时间戳获取盐值
        String salt = String.valueOf(now.getTime());
        //得到经过RSA加密的密码
        String password = user.getPassword();
        String rawPassword;
        try {
            rawPassword = RSAUtil.decrypt(password);
        } catch (Exception e) {
            throw new ConditionException("密码解密失败！");
        }
        //生成对应的md5的密码
        String md5Password = MD5Util.sign(rawPassword, salt, "UTF-8");
        user.setSalt(salt);
        user.setPassword(md5Password);
        user.setCreateTime(now);
        userDao.addUser(user);
        //添加用户信息
        UserInfo userInfo = new UserInfo();
        userInfo.setUserId(user.getId());
        userInfo.setNick(UserConstant.DEFAULT_NICK);
        userInfo.setBirth(UserConstant.DEFAULT_BIRTH);
        userInfo.setGender(UserConstant.GENDER_MALE);
        userInfo.setCreateTime(now);
        userDao.addUserInfo(userInfo);
        //添加用户默认权限角色
        userAuthService.addUserDefaultRole(user.getId());
    }
```

### 双令牌实现登录升级

#### 双token校验机制

**场景设想：**
 用户正在app或者应用中操作 token突然过期，此时用户不得不返回登陆界面，重新进行一次登录，这种体验性不好，于是引入双token校验机制

*使用：*
首次登陆时服务端返回两个token ，accessToken和refreshToken，accessToken过期时间比较短，refreshToken时间较长，且每次使用后会刷新，每次刷新后的refreshToken都是不同

*优势：*
 accessToken的存在，保证了登录态的正常验证，因其过期时间的短暂也保证了帐号的安全性

 refreshToekn的存在，保证了用户无需在短时间内进行反复的登陆操作来保证登录态的有效性，同时也保证了活跃用户的登录态可以一直存续而不需要进行重新登录，反复刷新也防止某些不怀好意的人获取refreshToken后对用户帐号进行动手动脚的操作

*流程*
登录操作，在后台服务器验证账号密码成功之后返回2个token—— accessToken和refreshToken。

在进行服务器请求的时候，先将Token发送验证，如果accessToken有效，则正常返回请求结果；如果accessToken无效，则验证refreshToken。

此时如果refreshToken有效则返回请求结果和新的accessToken和新的refreshToken。如果refreshToken无效，则提示用户进行重新登陆操作。

*流程图*

![image-20230225211213361-1677480184230-2](https://raw.githubusercontent.com/PeiyuChen1213/JAVA_Learning_Note/master/img/image-20230227170622739-1677488880757-3-1677489974134-9-1677492151172-2.png)



*相关代码*

Api

```java
 /**
     * 双token登录
     *
     * @param user
     * @return
     * @throws Exception
     */
    @PostMapping("/user-dts")
    public JsonResponse<Map<String, Object>> loginForDts(@RequestBody User user) throws Exception {
        Map<String, Object> map = userService.loginForDts(user);
        return new JsonResponse<>(map);
    }

    /**
     * 退出登录 删除掉refreshToken
     * @param request
     * @return
     */
    @DeleteMapping("/refresh-tokens")
    public JsonResponse<String> logout(HttpServletRequest request) {
        String refreshToken = request.getHeader("refreshToken");
        Long userId = userSupport.getCurrentUserId();
        userService.logout(refreshToken, userId);
        return JsonResponse.success();
    }

    /**
     * 刷新接入token
     * @param request
     * @return
     * @throws Exception
     */
    @PostMapping("/access-tokens")
    public JsonResponse<String> refreshAccessToken(HttpServletRequest request) throws Exception {
        String refreshToken = request.getHeader("refreshToken");
        String accessToken = userService.refreshAccessToken(refreshToken);
        return new JsonResponse<>(accessToken);
    }
```

service

```java
  /**
     *  返回一个accessToken 和refreshToken 一个接入token一个刷新token
     *  接入token更多是登录当中使用的 有效期比较短
     *  刷新token是用于其他的请求的 有效期比较长
     * @param user
     * @return
     * @throws Exception
     */
    public Map<String, Object> loginForDts(User user) throws Exception {
        String phone = user.getPhone() == null ? "" : user.getPhone();
        String email = user.getEmail() == null ? "" : user.getEmail();
        if (StringUtils.isNullOrEmpty(phone) && StringUtils.isNullOrEmpty(email)) {
            throw new ConditionException("参数异常！");
        }
        User dbUser = userDao.getUserByPhoneOrEmail(phone, email);
        if (dbUser == null) {
            throw new ConditionException("当前用户不存在！");
        }
        String password = user.getPassword();
        String rawPassword;
        try {
            rawPassword = RSAUtil.decrypt(password);
        } catch (Exception e) {
            throw new ConditionException("密码解密失败！");
        }
        String salt = dbUser.getSalt();
        String md5Password = MD5Util.sign(rawPassword, salt, "UTF-8");
        if (!md5Password.equals(dbUser.getPassword())) {
            throw new ConditionException("密码错误！");
        }
        Long userId = dbUser.getId();
        String accessToken = TokenUtil.generateToken(userId);
        String refreshToken = TokenUtil.generateRefreshToken(userId);
        //保存refresh token到数据库 先删除后添加以实现更新的操作
        userDao.deleteRefreshTokenByUserId(userId);
        userDao.addRefreshToken(refreshToken, userId, new Date());
        Map<String, Object> result = new HashMap<>();
        result.put("accessToken", accessToken);
        result.put("refreshToken", refreshToken);
        return result;
    }

//    退出登录之后就删除掉refreshToken
    public void logout(String refreshToken, Long userId) {
        userDao.deleteRefreshToken(refreshToken, userId);
    }

    /**
     * 当refreshToken还没过期的时候可以用来刷新登录用的accessToken
     * @param refreshToken
     * @return
     * @throws Exception
     */
    public String refreshAccessToken(String refreshToken) throws Exception {
//        从数据库当中获取refreshToken
        RefreshTokenDetail refreshTokenDetail = userDao.getRefreshTokenDetail(refreshToken);
        if (refreshTokenDetail == null) {
            throw new ConditionException("555", "token过期！");
        }
//        重新生成新的accessToken
        Long userId = refreshTokenDetail.getUserId();
        return TokenUtil.generateToken(userId);
    }

    public String getRefreshTokenByUserId(Long userId) {
        return userDao.getRefreshTokenByUserId(userId);
    }
```

流程简述：

1. loginForDts用来登录，校验成功之后 服务端生成两个token 一个accessToken一个refreshToken
2. 生成的refreshToken保存在数据库当中，一旦退出登录就删除token
3. 如果accessToken过期了但是refreshToken没过期会调用refreshAccessToken方法刷新accessToken
4. 每次在请求服务端的资源的时候也会带上这两个token 服务端会验证这两个token的情况（上述流程图介绍了基本的流程）

## 第四章 视频功能相关开发

#### FASTDFS 文件系统+Nginx

**Nginx结合FastDFS实现文件资源HTTP访问**：

![image-20230227170622739](https://raw.githubusercontent.com/PeiyuChen1213/JAVA_Learning_Note/master/img/image-20230227170743226-1677488880758-4-1677489974134-10-1677492161632-5.png)

#### 文件断点续传的原理

**文件分片**



Controller层

```java
@RestController
public class FileController {

    @Autowired
    private FileService fileService;

    /**
     * 返回md5之后的字符串
     * @param file
     * @return
     * @throws Exception
     */
    @PostMapping("/md5files")
    public JsonResponse<String> getFileMD5(MultipartFile file) throws Exception {
        String fileMD5 = fileService.getFileMD5(file);
        return new JsonResponse<>(fileMD5);
    }


    /**
     * 文件切片
     * @param slice
     * @param fileMd5
     * @param sliceNo
     * @param totalSliceNo
     * @return
     * @throws Exception
     */
    @PutMapping("/file-slices")
    public JsonResponse<String> uploadFileBySlices(MultipartFile slice,
                                                   String fileMd5,
                                                   Integer sliceNo,
                                                   Integer totalSliceNo) throws Exception {
        String filePath = fileService.uploadFileBySlices(slice, fileMd5, sliceNo, totalSliceNo);
        return new JsonResponse<>(filePath);
    }

}
```

service 层

```Java
@Service
public class FileService {

    @Autowired
    private FileDao fileDao;

    @Autowired
    private FastDFSUtil fastDFSUtil;

    public String uploadFileBySlices(MultipartFile slice,
                                         String fileMD5,
                                         Integer sliceNo,
                                         Integer totalSliceNo) throws Exception {
//      实现秒传功能 逻辑: 上传文件的同时会根据md5 将文件的地址也放到数据库当中去
        File dbFileMD5 = fileDao.getFileByMD5(fileMD5);
//      如果之前上传过相关的数据,就直接返回地址
        if(dbFileMD5 != null){
            return dbFileMD5.getUrl();
        }
        String url = fastDFSUtil.uploadFileBySlices(slice, fileMD5, sliceNo, totalSliceNo);
        if(!StringUtil.isNullOrEmpty(url)){
            dbFileMD5 = new File();
            dbFileMD5.setCreateTime(new Date());
            dbFileMD5.setMd5(fileMD5);
            dbFileMD5.setUrl(url);
            dbFileMD5.setType(fastDFSUtil.getFileType(slice));
//            将文件路径地址传到字符串当中去
            fileDao.addFile(dbFileMD5);
        }
        return url;
    }
```

FastDFS工具类当中的相关方法

```Java
   //上传可以断点续传的文件 -- 用于第一片文件上传
    public String uploadAppenderFile(MultipartFile file) throws Exception {
        String fileType = this.getFileType(file);
        StorePath storePath = appendFileStorageClient.uploadAppenderFile(DEFAULT_GROUP, file.getInputStream(), file.getSize(), fileType);
        return storePath.getPath();
    }

    //后续的文件上传 可以使用这个方法
    public void modifyAppenderFile(MultipartFile file, String filePath, long offset) throws Exception {
        appendFileStorageClient.modifyFile(DEFAULT_GROUP, filePath, file.getInputStream(), file.getSize(), offset);
    }

    public String uploadFileBySlices(MultipartFile file, String fileMd5, Integer sliceNo, Integer totalSliceNo) throws Exception {
        if (file == null || sliceNo == null || totalSliceNo == null) {
            throw new ConditionException("参数异常！");
        }
        String pathKey = PATH_KEY + fileMd5;
        String uploadedSizeKey = UPLOADED_SIZE_KEY + fileMd5;
        String uploadedNoKey = UPLOADED_NO_KEY + fileMd5;
//        查看当前已经上传的数据大小
        String uploadedSizeStr = redisTemplate.opsForValue().get(uploadedSizeKey);
        Long uploadedSize = 0L;
        if (!StringUtil.isNullOrEmpty(uploadedSizeStr)) {
            //查看已经上传了多少的内容
            uploadedSize = Long.valueOf(uploadedSizeStr);
        }
        if (sliceNo == 1) { //上传的是第一个分片
            String path = this.uploadAppenderFile(file);
            if (StringUtil.isNullOrEmpty(path)) {
                throw new ConditionException("上传失败！");
            }
            //记录已经上传的位置
            redisTemplate.opsForValue().set(pathKey, path);
            redisTemplate.opsForValue().set(uploadedNoKey, "1");
        } else {
            //从redis查询到该文件的pathKey
            String filePath = redisTemplate.opsForValue().get(pathKey);
            if (StringUtil.isNullOrEmpty(filePath)) {
                throw new ConditionException("上传失败！");
            }
            //上传这个分片
            this.modifyAppenderFile(file, filePath, uploadedSize);
            // 更新已经上传的片数
            redisTemplate.opsForValue().increment(uploadedNoKey);
        }
        // 修改历史上传分片文件大小
        uploadedSize += file.getSize();
        redisTemplate.opsForValue().set(uploadedSizeKey, String.valueOf(uploadedSize));
        //如果所有分片全部上传完毕，则清空redis里面相关的key和value
        String uploadedNoStr = redisTemplate.opsForValue().get(uploadedNoKey);
        Integer uploadedNo = Integer.valueOf(uploadedNoStr);
        String resultPath = "";
        if (uploadedNo.equals(totalSliceNo)) {
            resultPath = redisTemplate.opsForValue().get(pathKey);
            List<String> keyList = Arrays.asList(uploadedNoKey, pathKey, uploadedSizeKey);
//            清空redis
            redisTemplate.delete(keyList);
        }
        return resultPath;
    }
```

文件的切片的代码

```java
 public void convertFileToSlices(MultipartFile multipartFile) throws Exception {
        String fileType = this.getFileType(multipartFile);
        //生成临时文件，将MultipartFile转为File
        File file = this.multipartFileToFile(multipartFile);
        long fileLength = file.length();
        int count = 1;
        // 开始切片
        for (int i = 0; i < fileLength; i += SLICE_SIZE) {
            RandomAccessFile randomAccessFile = new RandomAccessFile(file, "r");
            randomAccessFile.seek(i);
            byte[] bytes = new byte[SLICE_SIZE];
            int len = randomAccessFile.read(bytes);
            String path = "/Users/hat/tmpfile/" + count + "." + fileType;
            File slice = new File(path);
            FileOutputStream fos = new FileOutputStream(slice);
            fos.write(bytes, 0, len);
            fos.close();
            randomAccessFile.close();
            count++;
        }
        //删除临时文件
        file.delete();
    }
```

断点续传的原理介绍：

1. 首先先获取文件的MD5
2. 其次进行文件分片，将文件细分成很多小片
3. 将分片后的文件，再逐一上传
4. 上传的细节，将上传分片的序号保存至redis当中，每次上次的时候再加1直至全部上传
5. 分片上传之后，将文件的MD5保存至数据库当中

#### 秒传的原理

秒传的逻辑十分简单，就是每次上传成功之后会将文件的md5保存到数据库当中，如果相同的文件再次上传会查询对应的数据库如果发现数据库当中有则直接发挥对应的path地址

```Java
//      实现秒传功能 逻辑: 上传文件的同时会根据md5 将文件的地址也放到数据库当中去
        File dbFileMD5 = fileDao.getFileByMD5(fileMD5);
//      如果之前上传过相关的数据,就直接返回地址
        if(dbFileMD5 != null){
            return dbFileMD5.getUrl();
        }
```

#### 视频投稿 

controller层

```Java
public class VideoController {
    @Autowired
    private VideoService videoService;

    @Autowired
    private UserSupport userSupport;

    @Autowired
    private ElasticSearchService elasticSearchService;

    /**
     * 视频投稿
     */
    @PostMapping("/videos")
    public JsonResponse<String> addVideos(@RequestBody Video video){
        Long userId = userSupport.getCurrentUserId();
        video.setUserId(userId);
        videoService.addVideos(video);
        //在es当中同步添加一条视频数据
        elasticSearchService.addVideo(video);
        return JsonResponse.success();
    }

```

service

```Java

    public void addVideo(Video video){
        videoRepository.save(video);
    }
```



#### 视频的瀑布流的开发（实际上就是分页查询）

controller层

```Java
  /**
     * 分页查询视频列表
     */
    @GetMapping("/videos")
    public JsonResponse<PageResult<Video>> pageListVideos(Integer size, Integer no, String area){
        PageResult<Video> result = videoService.pageListVideos(size, no ,area);
        return new JsonResponse<>(result);
    }
```

service层

```Java
   public PageResult<Video> pageListVideos(Integer size, Integer no, String area) {
        if(size == null || no == null){
            throw new ConditionException("参数异常！");
        }
        Map<String, Object> params = new HashMap<>();
        params.put("start", (no-1)*size);
        params.put("limit", size);
        params.put("area" , area);
        List<Video> list = new ArrayList<>();
        Integer total = videoDao.pageCountVideos(params);
        if(total > 0){
            list = videoDao.pageListVideos(params);
        }
        return new PageResult<>(total, list);
    }
```

#### 视频播放（分片实现，比较原始现在都用VOD了）

```java
  /**
     * 视频在线播放
     */
    @GetMapping("/video-slices")
    public void viewVideoOnlineBySlices(HttpServletRequest request,
                                        HttpServletResponse response,
                                        String url) {
        videoService.viewVideoOnlineBySlices(request, response, url);
    }
```

```Java
    public void viewVideoOnlineBySlices(HttpServletRequest request,
                                        HttpServletResponse response,
                                        String url) {
        try{
            fastDFSUtil.viewVideoOnlineBySlices(request, response, url);
        }catch (Exception ignored){}
    }

```

```Java
   /**
     * 视频分片在线观看
     * @param request
     * @param response
     * @param path
     * @throws Exception
     */
    public void viewVideoOnlineBySlices(HttpServletRequest request,
                                        HttpServletResponse response,
                                        String path) throws Exception {
        FileInfo fileInfo = fastFileStorageClient.queryFileInfo(DEFAULT_GROUP, path);
        long totalFileSize = fileInfo.getFileSize();
//        获取访问路径
        String url = httpFdfsStorageAddr + path;
        Enumeration<String> headerNames = request.getHeaderNames();
        Map<String, Object> headers = new HashMap<>();
        while (headerNames.hasMoreElements()) {
            String header = headerNames.nextElement();
            headers.put(header, request.getHeader(header));
        }
        String rangeStr = request.getHeader("Range");
        String[] range;
        if (StringUtil.isNullOrEmpty(rangeStr)) {
            rangeStr = "bytes=0-" + (totalFileSize - 1);
        }
        range = rangeStr.split("bytes=|-");
        long begin = 0;
        if (range.length >= 2) {
            begin = Long.parseLong(range[1]);
        }
        long end = totalFileSize - 1;
        if (range.length >= 3) {
            end = Long.parseLong(range[2]);
        }
        long len = (end - begin) + 1;
        String contentRange = "bytes " + begin + "-" + end + "/" + totalFileSize;
        response.setHeader("Content-Range", contentRange);
        response.setHeader("Accept-Ranges", "bytes");
        response.setHeader("Content-Type", "video/mp4");
        response.setContentLength((int) len);
        response.setStatus(HttpServletResponse.SC_PARTIAL_CONTENT);
        HttpUtil.get(url, headers, response);
    }
```

#### 视频点赞，收藏，投币，评论，详情

数据库表结构

```sql
DROP TABLE IF EXISTS `t_video`;
CREATE TABLE `t_video`  (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `userId` bigint NOT NULL COMMENT '用户id',
  `url` varchar(500) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '视频链接',
  `thumbnail` varchar(500) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '封面链接',
  `title` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '视频标题',
  `type` varchar(5) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '视频类型：0原创 1转载',
  `duration` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '视频时长',
  `area` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '所在分区：0鬼畜 1音乐 2电影',
  `description` text CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL COMMENT '视频简介',
  `createTime` datetime NULL DEFAULT NULL COMMENT '创建时间',
  `updateTime` datetime NULL DEFAULT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 26 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci COMMENT = '视频投稿记录表' ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for t_video_coin
-- ----------------------------
DROP TABLE IF EXISTS `t_video_coin`;
CREATE TABLE `t_video_coin`  (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '视频投稿id',
  `userId` bigint NULL DEFAULT NULL COMMENT '用户id',
  `videoId` bigint NULL DEFAULT NULL COMMENT '视频投稿id',
  `amount` int NULL DEFAULT NULL COMMENT '投币数',
  `createTime` datetime NULL DEFAULT NULL COMMENT '创建时间',
  `updateTime` datetime NULL DEFAULT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 3 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci COMMENT = '视频硬币表' ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for t_video_collection
-- ----------------------------
DROP TABLE IF EXISTS `t_video_collection`;
CREATE TABLE `t_video_collection`  (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `videoId` bigint NULL DEFAULT NULL COMMENT '视频投稿id',
  `userId` bigint NULL DEFAULT NULL COMMENT '用户id',
  `groupId` bigint NULL DEFAULT NULL COMMENT '收藏分组id',
  `createTime` datetime NULL DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 1 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci COMMENT = '视频收藏记录表' ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for t_video_comment
-- ----------------------------
DROP TABLE IF EXISTS `t_video_comment`;
CREATE TABLE `t_video_comment`  (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `videoId` bigint NOT NULL COMMENT '视频id',
  `userId` bigint NOT NULL COMMENT '用户id',
  `comment` text CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '评论',
  `replyUserId` bigint NULL DEFAULT NULL COMMENT '回复用户id',
  `rootId` bigint NULL DEFAULT NULL COMMENT '根节点评论id',
  `createTime` datetime NULL DEFAULT NULL COMMENT '创建时间',
  `updateTime` datetime NULL DEFAULT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 7 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci COMMENT = '视频评论表' ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for t_video_like
-- ----------------------------
DROP TABLE IF EXISTS `t_video_like`;
CREATE TABLE `t_video_like`  (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `userId` bigint NOT NULL COMMENT '用户id',
  `videoId` bigint NOT NULL COMMENT '视频投稿id',
  `createTime` datetime NULL DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 3 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci COMMENT = '视频点赞记录表' ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for t_video_tag
-- ----------------------------
DROP TABLE IF EXISTS `t_video_tag`;
CREATE TABLE `t_video_tag`  (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `videoId` bigint NOT NULL COMMENT '视频id',
  `tagId` bigint NOT NULL COMMENT '标签id',
  `createTime` datetime NULL DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 32 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci COMMENT = '视频标签关联表' ROW_FORMAT = Dynamic;

SET FOREIGN_KEY_CHECKS = 1;

```

点赞相关的接口

```java
/**
     * 点赞视频
     */
    @PostMapping("/video-likes")
    public JsonResponse<String> addVideoLike(@RequestParam Long videoId){
        Long userId = userSupport.getCurrentUserId();
        videoService.addVideoLike(videoId, userId);
        return JsonResponse.success();
    }

    /**
     * 取消点赞视频
     */
    @DeleteMapping("/video-likes")
    public JsonResponse<String> deleteVideoLike(@RequestParam Long videoId){
        Long userId = userSupport.getCurrentUserId();
        videoService.deleteVideoLike(videoId, userId);
        return JsonResponse.success();
    }

    /**
     * 查询视频点赞数量
     */
    @GetMapping("/video-likes")
    public JsonResponse<Map<String, Object>> getVideoLikes(@RequestParam Long videoId){
        Long userId = null;
        try{
            userId = userSupport.getCurrentUserId();
        }catch (Exception ignored){}
        Map<String, Object> result = videoService.getVideoLikes(videoId, userId);
        return new JsonResponse<>(result);
    }
```

收藏相关的接口

```Java

    /**
     * 收藏视频
     */
    @PostMapping("/video-collections")
    public JsonResponse<String> addVideoCollection(@RequestBody VideoCollection videoCollection){
        Long userId = userSupport.getCurrentUserId();
        videoService.addVideoCollection(videoCollection, userId);
        return JsonResponse.success();
    }

    /**
     * 取消收藏视频
     */
    @DeleteMapping("/video-collections")
    public JsonResponse<String> deleteVideoCollection(@RequestParam Long videoId){
        Long userId = userSupport.getCurrentUserId();
        videoService.deleteVideoCollection(videoId, userId);
        return JsonResponse.success();
    }

    /**
     * 查询视频收藏数量
     */
    @GetMapping("/video-collections")
    public JsonResponse<Map<String, Object>> getVideoCollections(@RequestParam Long videoId){
        Long userId = null;
        try{
            userId = userSupport.getCurrentUserId();
        }catch (Exception ignored){}
        Map<String, Object> result = videoService.getVideoCollections(videoId, userId);
        return new JsonResponse<>(result);
    }
```

上述都是比较简单的增删改查 没啥重要的

投币相关的接口

```java

    /**
     * 视频投币
     */
    @PostMapping("/video-coins")
    public JsonResponse<String> addVideoCoins(@RequestBody VideoCoin videoCoin){
        Long userId = userSupport.getCurrentUserId();
        videoService.addVideoCoins(videoCoin, userId);
        return JsonResponse.success();
    }

    /**
     * 查询视频投币数量
     */
    @GetMapping("/video-coins")
    public JsonResponse<Map<String, Object>> getVideoCoins(@RequestParam Long videoId){
        Long userId = null;
        try{
            userId = userSupport.getCurrentUserId();
        }catch (Exception ignored){}
        Map<String, Object> result = videoService.getVideoCoins(videoId, userId);
        return new JsonResponse<>(result);
    }
```

service层

```Java
 @Transactional
    public void addVideoCoins(VideoCoin videoCoin, Long userId) {
        Long videoId = videoCoin.getVideoId();
        Integer amount = videoCoin.getAmount();
        if(videoId == null){
            throw new ConditionException("参数异常！");
        }
        Video video = videoDao.getVideoById(videoId);
        if(video == null){
            throw new ConditionException("非法视频！");
        }
        //查询当前登录用户是否拥有足够的硬币
        Integer userCoinsAmount = userCoinService.getUserCoinsAmount(userId);
        userCoinsAmount = userCoinsAmount == null ? 0 : userCoinsAmount;
        if(amount > userCoinsAmount){
            throw new ConditionException("硬币数量不足！");
        }
        //查询当前登录用户对该视频已经投了多少硬币
        VideoCoin dbVideoCoin = videoDao.getVideoCoinByVideoIdAndUserId(videoId, userId);
        //新增视频投币
        if(dbVideoCoin == null){
            videoCoin.setUserId(userId);
            videoCoin.setCreateTime(new Date());
            videoDao.addVideoCoin(videoCoin);
        }else{
            Integer dbAmount = dbVideoCoin.getAmount();
            dbAmount += amount;
            //更新视频投币
            videoCoin.setUserId(userId);
            videoCoin.setAmount(dbAmount);
            videoCoin.setUpdateTime(new Date());
            videoDao.updateVideoCoin(videoCoin);
        }
        //更新用户当前硬币总数
        userCoinService.updateUserCoinsAmount(userId, (userCoinsAmount-amount));
    }
```

视频评论相关接口

```java
/**
     * 添加视频评论
     */
    @PostMapping("/video-comments")
    public JsonResponse<String> addVideoComment(@RequestBody VideoComment videoComment){
        Long userId = userSupport.getCurrentUserId();
        videoService.addVideoComment(videoComment, userId);
        return JsonResponse.success();
    }

    /**
     * 分页查询视频评论
     */
    @GetMapping("/video-comments")
    public JsonResponse<PageResult<VideoComment>> pageListVideoComments(@RequestParam Integer size,
                                                                        @RequestParam Integer no,
                                                                        @RequestParam Long videoId){
        PageResult<VideoComment> result = videoService.pageListVideoComments(size, no, videoId);
        return new JsonResponse<>(result);
    }

```

service层

```Java
 public PageResult<VideoComment> pageListVideoComments(Integer size, Integer no, Long videoId) {
        Video video = videoDao.getVideoById(videoId);
        if(video == null){
            throw new ConditionException("非法视频！");
        }
        Map<String, Object> params = new HashMap<>();
        params.put("start", (no-1)*size);
        params.put("limit", size);
        params.put("videoId", videoId);
        Integer total = videoDao.pageCountVideoComments(params);
        List<VideoComment> list = new ArrayList<>();
        if(total > 0){
            list = videoDao.pageListVideoComments(params);
            //批量查询二级评论
            List<Long> parentIdList = list.stream().map(VideoComment::getId).collect(Collectors.toList());
            List<VideoComment> childCommentList = videoDao.batchGetVideoCommentsByRootIds(parentIdList);
            //批量查询用户信息
            Set<Long> userIdList = list.stream().map(VideoComment::getUserId).collect(Collectors.toSet());
            Set<Long> replyUserIdList = childCommentList.stream().map(VideoComment::getUserId).collect(Collectors.toSet());
            Set<Long> childUserIdList = childCommentList.stream().map(VideoComment::getReplyUserId).collect(Collectors.toSet());
            userIdList.addAll(replyUserIdList);
            userIdList.addAll(childUserIdList);
            List<UserInfo> userInfoList = userService.batchGetUserInfoByUserIds(userIdList);
            Map<Long, UserInfo> userInfoMap = userInfoList.stream().collect(Collectors.toMap(UserInfo :: getUserId, userInfo -> userInfo));
            list.forEach(comment -> {
                Long id = comment.getId();
                List<VideoComment> childList = new ArrayList<>();
                childCommentList.forEach(child -> {
                    if(id.equals(child.getRootId())){
                        child.setUserInfo(userInfoMap.get(child.getUserId()));
                        child.setReplyUserInfo(userInfoMap.get(child.getReplyUserId()));
                        childList.add(child);
                    }
                });
                comment.setChildList(childList);
                comment.setUserInfo(userInfoMap.get(comment.getUserId()));
            });
        }
        return new PageResult<>(total, list);
    }
```



#### 视频的在线播放



#### 弹幕系统的开发（websocket协议实现）

websocket的报文体积小，支持长连接

![image-20230508222053414](./仿b站项目复盘与总结.assets/image-20230508222053414.png)

##### SpringBoot整合websocket



##### 多例模式下的bean注入 

多例模式下bean并不会重复注入，因为bean注入默认是只注入一次，不会二次注入

解决方法：使用启动类当中的SpringApplicationContext来获取bean



##### 弹幕系统的实现

核心还是websocket的使用

##### 弹幕推送性能优化

弹幕群发的性能优化：使用MQ将消息推送到前端 



##### 弹幕异步储存

springboot的@Asyn注解的使用 使用@Asyn标注一个异步的保存数据库的新增方法 这样后台就会异步的储存弹幕

储存到redis不需要使用异步的方式，因为redis的并发量可以承受，并且redis是串行化操作无需担心

##### 在线人数统计

springboot定时任务统计每个视频的在线人数

##### 弹幕查询

简单的数据库条件查询，但是根据是否是游客登录进行过滤

## 第五章 全局开发（主要是添加搜索功能）

这一章主要完成两个搜索功能： 一个是视频的搜索，一个是用户信息的搜索

### springboot整合es 并且实现视频搜索

### 全文搜索的实现（根据关键字可以查询到对应的视频和用户信息）

### 添加观看记录（游客模式下和登录模式下）

### 内容推荐（使用Apache的现场的协同算法库 mhout)

