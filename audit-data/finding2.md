[S-#] TITLE 'PasswordStore::setPassword' has no access control ,meaning a non-owner cloud change the password //突出non-owner 并没有对onwer进行访问控制
Description:
The setPassword is not  control access  this setPassword allows only the owner to setPassword a new password
```
 function setPassword(string memory newPassword) external {
        //@Audit-There are no Access Controls
        s_password = newPassword; //这个password是否能经过人为的更改 不是这个所有者也可以设置密码呢？
        emit SetNetPassword();
    }
```
Impact:
  任何人都可以改变密码 达到破坏合约的效果
 Anyone can set/change the stored password.severely breaking the contract`s intended functionality
Proof of Concept:

Recommended Mitigation: