## 示例代码
```java
package com.lch.study.demo;

import java.util.*;
import java.util.concurrent.ScheduledThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

/**
 * <p></p>
 *
 * @author lichanghong  create by  2019/7/29 13:41
 **/
public class GreenHouseScheduler {
    private volatile boolean light = false;
    private volatile boolean water = false;
    //恒温器
    private String thermostat = "Day";

    public synchronized String getThermostat() {
        return thermostat;
    }

    public synchronized void setThermostat(String thermostat) {
        this.thermostat = thermostat;
    }

    ScheduledThreadPoolExecutor scheduler = new ScheduledThreadPoolExecutor(10);

    public void schedule(Runnable event, long delay) {
        scheduler.schedule(event, delay, TimeUnit.MILLISECONDS);
    }

    public void repeat(Runnable event, long initialDelay, long period) {
        scheduler.scheduleAtFixedRate(event, initialDelay, period, TimeUnit.MILLISECONDS);
    }

    class LightOn implements Runnable {
        @Override
        public void run() {
            System.out.println("打开灯");
            light = true;
        }
    }

    class LightOff implements Runnable {
        @Override
        public void run() {
            System.out.println("关闭灯");
            light = false;
        }
    }

    class WaterOn implements Runnable {
        @Override
        public void run() {
            System.out.println("打开水龙头");
            water = true;
        }
    }

    class WaterOff implements Runnable {
        @Override
        public void run() {
            System.out.println("关闭水龙头");
            water = false;
        }
    }

    class ThermostatNight implements Runnable{

        @Override
        public void run() {
            System.out.println("设置夜晚模式");
            setThermostat("night");
        }
    }

    class ThermostatDay implements Runnable{
        @Override
        public void run() {
            System.out.println("设置白天模式");
            setThermostat("Day");
        }
    }

    class Bell implements Runnable{
        @Override
        public void run() {
            System.out.println("Bing!");
        }
    }
    //终止
    class Terminate implements  Runnable{
        @Override
        public void run() {
            System.out.println("Terminating");
            scheduler.shutdownNow();
            //必须启动一个独立的任务，因为调度服务已经关闭
            new Thread(){
                @Override
                public void run(){
                    for(DataPoint d : data){
                        System.out.println(d);
                    }
            }
            }.start();
        }
    }

    //新的特征数据收集
    static class DataPoint{
        final Calendar time;
        final float temperature;
        final float humidity;

        public DataPoint(Calendar d, float temp, float hum) {
            time = d;
            temperature = temp;
            humidity = hum;
        }

        @Override
        public String toString() {
            return time.getTime() +
                    String.format(
                            " temperature: %1$.1f humidity: %2$.2f",
                            temperature, humidity);
        }
    }


    private Calendar lastTime = Calendar.getInstance();
        {
            //调试时间为半小时
            lastTime.set(Calendar.MINUTE, 30);
            lastTime.set(Calendar.SECOND, 00);
        }

        private float lastTemp = 65.0f;
        //临时放心
        private int temDirection = +1;
        //最后的温度
        private float lastHumidity = 50.0f;

        private int humidityDirection = +1;
        List<DataPoint> data = Collections.synchronizedList(new ArrayList<>());
        private Random rand = new Random(47);
        class CollectData implements Runnable{
            @Override
            public void run() {
                System.out.println("Collecting data");
                synchronized (GreenHouseScheduler.this){
                    //假装时间间隔比他长
                    lastTime.set(Calendar.MINUTE,lastTime.get(Calendar.MINUTE)+30 );
                    // 1/5的机会转变方向
                    if(rand.nextInt(5)==4){
                        temDirection=-temDirection;
                    }
                    lastTemp = lastTemp + (temDirection * (1.0f + rand.nextFloat()));
                    if(rand.nextInt(5)==4){
                        humidityDirection=-humidityDirection;
                    }
                    lastHumidity = lastHumidity+lastHumidity*rand.nextFloat();
                    data.add(new DataPoint((Calendar) lastTime.clone(), lastTemp, lastHumidity));
                }
            }
        }
    public static void main(String[] args) {
        GreenHouseScheduler gh = new GreenHouseScheduler();
        gh.schedule(gh.new Terminate(), 5000);
        // Former "Restart" class not necessary:
        gh.repeat(gh.new Bell(), 0, 1000);
        gh.repeat(gh.new ThermostatNight(), 0, 2000);
        gh.repeat(gh.new LightOn(), 0, 200);
        gh.repeat(gh.new LightOff(), 0, 400);
        gh.repeat(gh.new WaterOn(), 0, 600);
        gh.repeat(gh.new WaterOff(), 0, 800);
        gh.repeat(gh.new ThermostatDay(), 0, 1400);
        gh.repeat(gh.new CollectData(), 500, 500);
    }


}


```

## UseParallelGC

JDK7之后默认的垃圾回收器，JDK9之后可能是G1
```
-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log
```
gc日志
```
Java HotSpot(TM) 64-Bit Server VM (25.181-b13) for windows-amd64 JRE (1.8.0_181-b13), built on Jul  7 2018 04:01:33 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16658208k(8421896k free), swap 25571104k(16282728k free)
CommandLine flags: -XX:InitialHeapSize=266531328 -XX:MaxHeapSize=4264501248 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC 
Heap
 PSYoungGen      total 76288K, used 20978K [0x000000076b400000, 0x0000000770900000, 0x00000007c0000000)
  eden space 65536K, 32% used [0x000000076b400000,0x000000076c87c858,0x000000076f400000)
  from space 10752K, 0% used [0x000000076fe80000,0x000000076fe80000,0x0000000770900000)
  to   space 10752K, 0% used [0x000000076f400000,0x000000076f400000,0x000000076fe80000)
 ParOldGen       total 175104K, used 0K [0x00000006c1c00000, 0x00000006cc700000, 0x000000076b400000)
  object space 175104K, 0% used [0x00000006c1c00000,0x00000006c1c00000,0x00000006cc700000)
 Metaspace       used 4544K, capacity 4812K, committed 4992K, reserved 1056768K
  class space    used 503K, capacity 564K, committed 640K, reserved 1048576K
```
年轻代和老年代都是并行执行的且独占的，老年代也执行了压缩操作。
适用于多核CPU且使用了较大内存空间的场景。

## JVM 默认添加的参数
```
 -XX:InitialHeapSize=266531328 -XX:MaxHeapSize=4264501248 -XX:+PrintGC -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC 
```
`-XX:InitialHeapSize`:-Xms 初始化堆大小<br> 
`-XX:MaxHeapSize`:-Xmx 最大堆内存大小<br>
`-XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation` 和OOP相关<br>
`-XX:+UseParallelGC ` 没有指定GC时，java8默认的垃圾回收器。<br>
## UseSerialGC

```
-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log -XX:+UseSerialGC
```
GC日志
```
Java HotSpot(TM) 64-Bit Server VM (25.181-b13) for windows-amd64 JRE (1.8.0_181-b13), built on Jul  7 2018 04:01:33 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16658208k(9088528k free), swap 25571104k(16015536k free)
CommandLine flags: -XX:InitialHeapSize=266531328 -XX:MaxHeapSize=4264501248 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseSerialGC 
Heap
 def new generation   total 78656K, used 22390K [0x00000006c1c00000, 0x00000006c7150000, 0x0000000716800000)
  eden space 69952K,  32% used [0x00000006c1c00000, 0x00000006c31dda90, 0x00000006c6050000)
  from space 8704K,   0% used [0x00000006c6050000, 0x00000006c6050000, 0x00000006c68d0000)
  to   space 8704K,   0% used [0x00000006c68d0000, 0x00000006c68d0000, 0x00000006c7150000)
 tenured generation   total 174784K, used 0K [0x0000000716800000, 0x00000007212b0000, 0x00000007c0000000)
   the space 174784K,   0% used [0x0000000716800000, 0x0000000716800000, 0x0000000716800200, 0x00000007212b0000)
 Metaspace       used 4543K, capacity 4812K, committed 4992K, reserved 1056768K
  class space    used 503K, capacity 564K, committed 640K, reserved 1048576K

```
`-XX:+UseSerialGC`:新生代、老年代都使用串行垃圾回收器

独占的式，只有在嵌入式应用场景下或者`client`单CPU内存小的。

## UseParNewGC

```
-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log -XX:+UseParNewGC
```
控制台告警信息
```
Java HotSpot(TM) 64-Bit Server VM warning: Using the ParNew young collector with the Serial old collector is deprecated and will likely be removed in a future release
```
ParNew未来将不可用
<br>
日志
```
Java HotSpot(TM) 64-Bit Server VM (25.181-b13) for windows-amd64 JRE (1.8.0_181-b13), built on Jul  7 2018 04:01:33 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16658208k(9051508k free), swap 25571104k(16020272k free)
CommandLine flags: -XX:InitialHeapSize=266531328 -XX:MaxHeapSize=4264501248 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParNewGC 
Heap
 par new generation   total 78656K, used 22391K [0x00000006c1c00000, 0x00000006c7150000, 0x0000000716800000)
  eden space 69952K,  32% used [0x00000006c1c00000, 0x00000006c31ddef0, 0x00000006c6050000)
  from space 8704K,   0% used [0x00000006c6050000, 0x00000006c6050000, 0x00000006c68d0000)
  to   space 8704K,   0% used [0x00000006c68d0000, 0x00000006c68d0000, 0x00000006c7150000)
 tenured generation   total 174784K, used 0K [0x0000000716800000, 0x00000007212b0000, 0x00000007c0000000)
   the space 174784K,   0% used [0x0000000716800000, 0x0000000716800000, 0x0000000716800200, 0x00000007212b0000)
 Metaspace       used 4543K, capacity 4812K, committed 4992K, reserved 1056768K
  class space    used 503K, capacity 564K, committed 640K, reserved 1048576K
```
独占式
## UseParallelOldGC

```
-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log -XX:+UseParallelOldGC
```
日志信息
```
Java HotSpot(TM) 64-Bit Server VM (25.181-b13) for windows-amd64 JRE (1.8.0_181-b13), built on Jul  7 2018 04:01:33 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16658208k(8221580k free), swap 25571104k(14932728k free)
CommandLine flags: -XX:InitialHeapSize=266531328 -XX:MaxHeapSize=4264501248 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelOldGC 
Heap
 PSYoungGen      total 76288K, used 20978K [0x000000076b400000, 0x0000000770900000, 0x00000007c0000000)
  eden space 65536K, 32% used [0x000000076b400000,0x000000076c87c8f8,0x000000076f400000)
  from space 10752K, 0% used [0x000000076fe80000,0x000000076fe80000,0x0000000770900000)
  to   space 10752K, 0% used [0x000000076f400000,0x000000076f400000,0x000000076fe80000)
 ParOldGen       total 175104K, used 0K [0x00000006c1c00000, 0x00000006cc700000, 0x000000076b400000)
  object space 175104K, 0% used [0x00000006c1c00000,0x00000006c1c00000,0x00000006cc700000)
 Metaspace       used 4544K, capacity 4812K, committed 4992K, reserved 1056768K
  class space    used 503K, capacity 564K, committed 640K, reserved 1048576K

```
算法是为老年代设计的，它的执行步骤分为三步:
- 标记（mark）
- 总结(summary)
- 压缩(compaction)

## UseConcMarkSweepGC(CMS)

Concurrent Mark-Sweep(并发标记-清理)
```
-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log -XX:+UseConcMarkSweepGC
```
多线程执行
包括以下步骤：

- 初始标记(Initial-Mark)
- 并发标记(Concurrent-Mark)
- 再次标记(Remark)
- 并发清除(Concurrent-Sweep)

日志
```
Java HotSpot(TM) 64-Bit Server VM (25.181-b13) for windows-amd64 JRE (1.8.0_181-b13), built on Jul  7 2018 04:01:33 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16658208k(8842888k free), swap 25571104k(15640476k free)
CommandLine flags: -XX:InitialHeapSize=266531328 -XX:MaxHeapSize=4264501248 -XX:MaxNewSize=697933824 -XX:MaxTenuringThreshold=6 -XX:OldPLABSize=16 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseConcMarkSweepGC -XX:-UseLargePagesIndividualAllocation -XX:+UseParNewGC 
Heap
 par new generation   total 78656K, used 22392K [0x00000006c1c00000, 0x00000006c7150000, 0x00000006eb590000)
  eden space 69952K,  32% used [0x00000006c1c00000, 0x00000006c31de0c0, 0x00000006c6050000)
  from space 8704K,   0% used [0x00000006c6050000, 0x00000006c6050000, 0x00000006c68d0000)
  to   space 8704K,   0% used [0x00000006c68d0000, 0x00000006c68d0000, 0x00000006c7150000)
 concurrent mark-sweep generation total 174784K, used 0K [0x00000006eb590000, 0x00000006f6040000, 0x00000007c0000000)
 Metaspace       used 4546K, capacity 4812K, committed 4992K, reserved 1056768K
  class space    used 503K, capacity 564K, committed 640K, reserved 1048576K

```
新生代默认使用`UseParNewGC`

## G1

JDK11默认使用的是G1

```
-XX:+PrintGCDetails -verbose:gc -Xloggc:gc.log -XX:+UseG1GC
```
日志
```
Java HotSpot(TM) 64-Bit Server VM (25.181-b13) for windows-amd64 JRE (1.8.0_181-b13), built on Jul  7 2018 04:01:33 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16658208k(8704664k free), swap 25571104k(15460116k free)
CommandLine flags: -XX:InitialHeapSize=266531328 -XX:MaxHeapSize=4264501248 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
Heap
 garbage-first heap   total 262144K, used 7168K [0x00000006c1c00000, 0x00000006c1d00800, 0x00000007c0000000)
  region size 1024K, 8 young (8192K), 0 survivors (0K)
 Metaspace       used 4550K, capacity 4812K, committed 4992K, reserved 1056768K
  class space    used 503K, capacity 564K, committed 640K, reserved 1048576K


```

## 查看GC造成程序停顿时间

```java
-XX:+PrintGCDetails -verbose:gc -Xloggc:gc.log -XX:+UseG1GC 
-XX:+PrintGCApplicationStoppedTime -XX:+PrintGCApplicationConcurrentTime 
```

`PrintGCApplicationConcurrentTime` ： 程序执行时间

`PrintGCApplicationStoppedTime` : 打印GC时造成程序停顿时间

日志

```java
Java HotSpot(TM) 64-Bit Server VM (25.181-b13) for windows-amd64 JRE (1.8.0_181-b13), built on Jul  7 2018 04:01:33 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16658208k(8386380k free), swap 25571104k(15267628k free)
CommandLine flags: -XX:InitialHeapSize=266531328 -XX:MaxHeapSize=4264501248 -XX:+PrintGC -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
1.050: Application time: 0.8700119 seconds
1.050: Total time for which application threads were stopped: 0.0000828 seconds, Stopping threads took: 0.0000299 seconds
3.051: Application time: 2.0008747 seconds
3.051: Total time for which application threads were stopped: 0.0000410 seconds, Stopping threads took: 0.0000210 seconds
4.199: Application time: 1.1482400 seconds
4.199: Total time for which application threads were stopped: 0.0000870 seconds, Stopping threads took: 0.0000256 seconds
5.234: Application time: 1.0344974 seconds
5.234: Total time for which application threads were stopped: 0.0001223 seconds, Stopping threads took: 0.0000919 seconds
Heap
 garbage-first heap   total 262144K, used 7168K [0x00000006c1c00000, 0x00000006c1d00800, 0x00000007c0000000)
  region size 1024K, 8 young (8192K), 0 survivors (0K)
 Metaspace       used 4550K, capacity 4812K, committed 4992K, reserved 1056768K
  class space    used 503K, capacity 564K, committed 640K, reserved 1048576K
5.235: Application time: 0.0006033 seconds

```

##  -XX:ConcGCThreads

设置Java并行执行GC线程的数量，默认为GC独占时运行线程的1/4。设置过大时会导致应用程序可使用CPU资源减少。过小时会增加GC并行循环执行时间。

```
-XX:+PrintGCDetails -verbose:gc -Xloggc:gc.log -XX:+UseG1GC  -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCApplicationConcurrentTime  -XX:ConcGCThreads=4
```

日志

```java
Java HotSpot(TM) 64-Bit Server VM (25.181-b13) for windows-amd64 JRE (1.8.0_181-b13), built on Jul  7 2018 04:01:33 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16658208k(8499180k free), swap 25571104k(15473588k free)
CommandLine flags: -XX:ConcGCThreads=4 -XX:InitialHeapSize=266531328 -XX:MaxHeapSize=4264501248 -XX:+PrintGC -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
1.043: Application time: 0.8816568 seconds
1.043: Total time for which application threads were stopped: 0.0000700 seconds, Stopping threads took: 0.0000216 seconds
3.044: Application time: 2.0011190 seconds
3.044: Total time for which application threads were stopped: 0.0000686 seconds, Stopping threads took: 0.0000341 seconds
4.182: Application time: 1.1374900 seconds
4.182: Total time for which application threads were stopped: 0.0000543 seconds, Stopping threads took: 0.0000156 seconds
5.206: Application time: 1.0244691 seconds
5.206: Total time for which application threads were stopped: 0.0000273 seconds, Stopping threads took: 0.0000156 seconds
Heap
 garbage-first heap   total 262144K, used 8192K [0x00000006c1c00000, 0x00000006c1d00800, 0x00000007c0000000)
  region size 1024K, 9 young (9216K), 0 survivors (0K)
 Metaspace       used 4543K, capacity 4812K, committed 4992K, reserved 1056768K
  class space    used 503K, capacity 564K, committed 640K, reserved 1048576K
5.207: Application time: 0.0004528 seconds

```

