# Unity Game（Unity开发）
## Coroutine（协程）
### 说明
协程同样运行在主线程上，但和主任务异步进行。一般当有某些任务需要分段完成（而不希望在一帧内做完）的时候需要用到

### 使用
#### 开始协程
```c#
IEnumerator enumerator = CreateObject();
StartCoroutine(enumerator);
```
调用StartCoroutine函数创建协程，参数是一个迭代器。Unity每帧会使用迭代器MoveNext访问当前存储的Current 一般也就是yield return所创建的YieldInstruction
#### 终止协程
协程在对应脚本unable时并不会停止，只有执行结束或者对应脚本绑定Go被销毁才会终止。
如果想要手动终止协程可以使用
```c#
IEnumerator enumerator = CreateObject();
StopCoroutine(enumerator);
```
#### yield
涉及到协程，一般都会用到yield return命令
可以停止该协程这帧代码的执行，并在下一个时间段继续执行剩下的代码
```c#
IEnumerator CreateObjects()
    {
        //开启嵌套协程
        IEnumerator enumerator = CreateObjects2();
        yield return StartCoroutine(enumerator);
        
        //下一帧继续
        yield return null;
        //创建GameObject A
        new GameObject("A");

        //0.5秒后继续
        yield return new WaitForSeconds(0.5f);
        //创建GameObject B
        new GameObject("B");
    }
```
需要注意的是，yield中断代码到下次执行的时候，是在每帧Update之后，LateUpdate之前
![Alt text](assets/unity_game/image-3.png)
#### 嵌套协程
可以通过yield return StartCoroutine(IEnumerator)开启嵌套协程。开启后会在<font color=#FF0000>同一帧</font>进入到对应协程的方法，直到<font color=#FF0000>嵌套协程执行结束</font>才会回到主协程继续执行剩余的任务
```c#
void Start()
    {
        IEnumerator enumerator = CreateObjects();
        StartCoroutine(enumerator);
        //StopCoroutine(enumerator);
    }

    IEnumerator CreateObjects()
    {
        //开启嵌套协程
        IEnumerator enumerator = CreateObjects2();
        yield return StartCoroutine(enumerator);
        
        //下一帧继续
        yield return null;
        //创建GameObject A
        new GameObject("A");

        //0.5秒后继续
        yield return new WaitForSeconds(0.5f);
        //创建GameObject B
        new GameObject("B");
    }
    
    IEnumerator CreateObjects2()
    {
        //1秒后继续
        yield return new WaitForSeconds(1f);
        //创建GameObject C
        new GameObject("C");
    }
```
创建顺序是"C" "A" "B"