### [S-#] Storing the password on-chain makes it visable to anyone ,and  no  longer private 
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

