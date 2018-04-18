## 相关issue讨论总结

- [ ] Validating docker 1.13.x and 17.03
https://github.com/kubernetes/kubernetes/issues/42926
- `yguo0905`  使用`benchmark tool`测试：`Docker 1.13.1 micro-benchmark result` 

使用`docker 1.13.1`与`docker 1.12.1`对比，`docker 1.13.1`在cpu使用率和时延都更优

使用`node e2e `和`cluster e2e测试`：`Docker 1.13 and 17.03 Validation Summary`
- `dchen1107`   我们已经完成了docker 1.13.x 和 17.03对k8s 1.8的认证。`疑问`：为什么说完成了认证？做了什么来进行认证的？
- `euank` 反驳：在1.8 release中，并不支持docker 17.03-ce. 原因是docker-ce release的周期只有4个月。使用k8s和其推荐的docker版本，却不能收到docker更新的安全推送，所以不建议社区做版本推荐。  对于更新的docker版本，可以通过新的issue进行跟踪和验证，但是其认为这不是一个好的解决方法。
- `tianon`提及：
> Additionally, as of last night, 17.06 is no longer officially supported in the open source editions of Docker (only 17.07 and now 17.09).

> Edit: @cpuguy83 was kind enough to correct me privately on this -- 17.06 is supported for one more month (the "stable" releases overlap for one month; see https://blog.docker.com/2017/03/docker-enterprise-edition/ which has a graphic that includes CE to illustrate this)

`疑问`：从链接中没看出来为什么不支持17.06了。

https://blog.docker.com/2017/03/docker-enterprise-edition （这里主要讲解了docker的版本控制方案）中提到docker api版本与docker的平台版本是独立的。docker api版本的迭代升级会更加谨慎，且后向兼容。
- `euank`  认为所有经过验证的docker版本都已经过时了。1.13 CSE（“商业支持版”）可能仍然会得到docker公司的支持，但这是docker的一个封闭源代码分支，而不是docker 1.13。
Kubernetes可能需要明确地不推荐引擎的特定版本，而是支持dockerd API的一个版本。然后对上游进行测试，以确保事情不会中断。
- `yujuhong` 认为，发布docker api版本更加合理。可以在dockershim中提供一个可选项来指定docker API版本。这样用户可以在相同的API版本基础上进行docker engine升级，但是遇到bug需要用户自己去解决。（`idea`：这样貌似可以减少因为docker升级引入的bug对k8s的修改。。。这个人提出的idea，有问题可以找他交流）。他并不认为认为Kubernetes在推荐docker。只不过docker恰好是我们现在拥有的唯一一个完整的、生产就绪的集成。====》关于使用docker API引入新的issue：RFC: Validate Docker against the Docker API versions https://github.com/kubernetes/kubernetes/issues/53221 

下面开始有讨论为什么选择是docker而不是cri-o或其他什么CRI。
- `wkornewald` :在每个Docker版本发布之前，是否有可能运行k8s中指定docker的测试套件，以确保不会发生重大改变或倒退？毕竟，Kubernetes是Docker的重要用例。（`idea`：这个是让docker去做的测试）
- `euank`：由于许多原因，一个项目承担运行其他项目的测试的负担是很少见的。（`idea`：好像docker要承担部分k8s的测试。重大负担啊。）
- `yujuhong`：我希望看到社区能够找到验证Docker RCs和报告问题的工作。我认为这与验证Docker（kubernetes）与固定API版本（53221）的验证是正交的。不知道Docker如何验证Kubernetes的发行@cpuguy83。
- `cpuguy83`：进行了kube node e2e的测试，要进一步进行cluster e2e测试（`idea`：应该是搞docker测试的，可以找他交流）
- `dawidmalina`:他使用的是docker ce 17/06.x，能正常工作（`idea`，可以找他确认用的k8s是什么版本的。现在是否还是用的17.06）Check https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.9.md section: External Dependencies.
- k8s 1.9：The validated docker versions are the same as for v1.8: 1.11.2 to 1.13.1 and 17.03.x
- k8s 1.10：The validated docker versions are the same as for v1.9: 1.11.2 to 1.13.1 and 17.03.x 

- [ ] RFC: Validate Docker against the Docker API versions
https://github.com/kubernetes/kubernetes/issues/53221
