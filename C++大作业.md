##### c++大作业
姓名：蔡卓 学号：18062010


##### 实验步骤

### 第一步
按照快速使用手册提示
通过编译源码的方式安装Nebula Graph
出现的问题：在虚拟机ubuntu18上构建Debug版本的过程中，使用make编译源码中途失败
之试了试分配给虚拟机更多内存后，发现编译成功。

### 第二步
对源码增加了一段代码如下
```
TEST(Duration,elapsedInMicroSeconds){
     Duration dur;
     for(int i=0;i<150;i++){
     dur.reset();
     auto start=std::chrono::steady_clock::now();
     usleep(3);
     auto diff=std::chrono::steady_clock::now()-start;
     dur.pause();
     ASSERT_LE(std::chrono::duration_cast<<std::chrono::microseconds>(diff).count()-2,
                dur.elapsedInUSec())<<"Inaccuracy in iteration"<<i;
     ASSERT_LE(std::chrono::duration_cast<<std::chrono::microseconds>(diff).count()+2,
                dur.elapsedInUSec())<<"Inaccuracy in iteration"<<i;
     }
 }
 ```
 最后提交成功。
 
 
 ###### 大作业总结体会
 这学科实在是太难了，老师开始就说了这个学科很难，老师前几节可就说了这门课讲的一些涉及到许多应用方面的和行业方面的事，
 对此我们都是跃跃欲试，之后的学习也有很多地方不懂，但通过这门课的学习和大作业的完成，我也学习到了更多方面的操作，对
 github的操作也是越来越熟悉，编程能力也有所提高，非常感谢老师的教导，虽然这门课很难，但是老师也给予了很多的帮助，
 带领我们进入了一个新的领域。
