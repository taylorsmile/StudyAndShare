## informix锁表处理的一般方法


可以通过onmode -u 将数据库服务器强行进入单用户模式，来释放被锁的表

对于正式运行的设备，该方法就不可取了，必须找到锁表的session或进程，然后根据具体情况进行处理

这里简单介绍一下如何根据被锁表找到锁表的进程及其session id

***

### 以INFORMIX用户登录后，具体操作步骤如下：

**1、取得当前锁情况**
```    
[informix] :/opt/informix/etc>onstat -k


Informix Dynamic Server Version 7.31.UC4    -- On-Line -- Up 07:53:32 -- 101328 Kbytes

Locks

address   wtlist    owner        lklist        type     tblsnum   rowid    key#/bsiz

c0f06c20    0      c60a363c        0          HDR+S     100002     204         0

c0f5f214    0      c60a363c     c0f06c20      HDR+IX    200020      0          0

c0f64528    0      c60a363c     c0f5f214      HDR+X     200020     100         0


3 active, 200000 total, 65536 hash buckets


根据tabnum（16进制的partnum）及查找systabnames表得到的partnum（10进制）和tabname的对应关系，可得到该锁的onwer，如 c60a363c ，实际上表示Userthread address。
```

**2、根据Userthread address得到session id和其他信息**
```
[informix] :/opt/informix/etc>onstat  -u

[informix] :/opt/informix>onstat -u | grep c60a363c

address    flags   sessid      user     tty     wait   tout locks nreads nwrites

c60a363c  Y-BP---   322      informix  ttyp1  c6203700   0    3     1        0


上面的322 即为session id
```
 
**3、根据sessid得到进程ID**
```
[informix] :/opt/informix/etc>onstat -g ses 322
 
 
Informix Dynamic Server Version 7.31.UC4    -- On-Line -- Up 10:13:44 -- 93136 Kbytes
 
session id      user      tty      pid     hostname   #RSAM threads   total memory   used memory

   322        informix   ttyp2    7817      smcp1          1             73728         63096

 
由此，可得到锁表进程的PID 7817
```

**4、查看包含7817的所有进程的相关信息**
```
[informix] :/opt/informix/etc>ps -ef | grep 7817

informix 12141 11613  0 19:09:51 ttyp3     0:00 grep 7817

informix  7817  7260  0 09:47:16 ttyp2     0:00 dbaccess test
```
 
由此，我们可得到锁表的进程相关信息，可根据实际锁表进程的重要程度的具体情况采取相映处理方法：

对于重要且该进程可以自动重联数据库的进程，可以用onmode -z 322（sesid）的方法杀掉锁表session，否则也可直接杀掉锁表的进程 kill -9 7817

通过上述过程，也可以比较清楚的理解维护中使用工具onstat的几个基本命令的使用
