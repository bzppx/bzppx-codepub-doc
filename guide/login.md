# 登录管理

> bzppx-codepub 的登录分为账号登录和其他登录，考虑到系统可能大多数情况是在公司内部使用，且公司内部一般有统一的邮箱账号登录体系，如何将公司
内部的账号体系（LDAP）与 bzppx-codepub 的系统结合起来，是我们在开发过程中优先考虑的问题。

## 账号登录

只需要在后台添加一个账号，用户即可登录使用系统

## 其他登录

### 添加认证

首先在 管理后台》系统管理》认证管理 的菜单下添加认证 api
- api名称: 认证 api 的名称，例如 LDAP, LDAP_HK 
- url: 第三方登录的认证接口地址，例如 http://xxxxxx.com/ldap/login
- key: 
- 排序号：管理员可以添加多个 api 认证体系，排序号决定着该认证 api 在其他登录页面显示的顺序

### 认证接口开发

认证接口必须满足如下要求

- 请求方式: POST
- 请求参数：username: 用户名， password: 密码
- 成功时返回：
```
{
    msg: "", 输出信息，成功时为空字符串，必须返回
    uid: "", 用户id，必须是字母数字或者下划线，如果有其他字符会登录失败，必须返回
    given_name: "", 用户名，字符串，必须返回
    email: "", 用户邮箱，没有留空
    mobile: "", 用户手机号，没有留空
}
```
- 失败返回：
```
{
    msg: "", 输出信息，失败时为失败原因，不能为空
}
```

### 认证api示例

- PHP

```
<?php
//获取数据
$username = $_POST["username"];
$password = $_POST["password"];
//连接数据库伪代码

$conn = mysql_connect();
$data = $conn->query("select * from user where username=" . $username);
//判断是否存在账号或者密码是否正确 （验证数据的正确性）
if (empty($data) || $data['password'] != $password) {
    $result['msg'] = "账号或密码错误！";
    echo json_encode($result, JSON_UNESCAPED_UNICODE);
    exit();

}
//uid, given_name, msg 必须有，如果为空填空字符串，email 和 mobile 可选返回
$result = [
    'msg' => '',
    'uid' => $data['uid'],
    'given_name' => $data['real_name'],
    'email' => $data['email'],
    'mobile' => $data["mobile"]
];
echo json_encode($result, JSON_UNESCAPED_UNICODE);
```