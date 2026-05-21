### [H-1] Storing the password on-chain makes it visable to anyone ,and  no  longer private 
[](https://github.com/Cyfrin/security-and-auditing-full-course-s23/blob/main/finding_layout.md#s--title-root-cause--impact)
 ROOT source and impact (根本原因及影响)
**Description:**
#这里放自己发现的东西真实发现的东西 比如 第一点 实际上private 看似存放了私有的代码其实不一定是私有的
人类依旧可以阅读他们 
链上的数据对于所有人而言都可见。他private 只针对的是接口部分并没有针对整个链条链路计划进行
所以 s_password对于整个而言都是一种可见的行为
all data stored on-chain is visible to anyone ,and can be read directly from the blockchain .The `PasswordStore::s_password` variable is intended to be a 
 accessed through the `PasswordStore::getPassword` function ,which is intended to be only called ,by the owner of the contract 
下面我们展示了一种从链外读取数据的方法，来展示这种数据丢失的可能性
**Impact:** 
每个人都能读取密码 让每个人都可以用 
**Proof of Concept:** 重点的POC 概念性证明问题！！！
重点在于这POC本身内容怎么写  相当于一个小的测试用例
**Recommended Mitigation:** 
在写建议 :
Due  to  this ,the overall architecture of the contract should be rethought .One could encrtpy the password off -chain and then store toe encrypted password on -chain tHIS WORLD REQUEIE THE USER TO REMEMBER ANTOHER PASSWORD OFF-CHAIN TO DERYPT THE PASSWORDD hOWEVER YOU'Deploy also likely want to remove the view function as you wouldn`t  wat the user to accidentally send a transactigon with the password tha decryptsyour password

Impact & Likelihood :
-impact:HIGH
-Likelihood:HIGH
-Severity:HIGH

[h-2] TITLE 'PasswordStore::setPassword' has no access control ,meaning a non-owner cloud change the password //突出non-owner 并没有对onwer进行访问控制
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
Likelihood && Impact :
--Impact:HIGH
--Likelihood:HIGH
--Severity:HIGH


###[L-1] The 'PasswordStore::getPassword' natspec indicates a parameter that doesn`t exist,casuing the natspec to be incorrect.    
Description:
```
   /*
     * @notice This allows only the owner to retrieve the password.
     * @param newPassword The new password to set.
     */
    //q getpassword内没有传入任何参数问题
    function getPassword() external view returns (string memory) {
```
The 'PasswordStore::getPassword' function signature is 'getPassword()' while the natspec says it should be 'getPassword(string)'.
//应该往里加入string 的字符串内容
Impact:The natspec is incorrect

Proof of Concept:
Recommended Mitigation: Remove the incorrect natspec line
##Likelihood && Impact :
--Impact:NONE
--Likelihood:NONE
--Severity:Information/GAS/Non-

this isn`t a bug  but you should know ....
