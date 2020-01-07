---
title: Spark源码阅读之一——SparkContext初始化
categories:
  - 大数据
  - Spark
description: Spark源码阅读之一——SparkContext初始化
abbrlink: 36cd8492
date: 2019-12-29 16:40:27
tags:
keywords:
---

## 一、SparkContext概述

　　SparkDriver用于提交用户应用程序，实际可以看作Spark的客户端。Spark Driver的初始化始终围绕着SparkContext的初始化。而SparkContext的配置参数则是由SparkConf负责，SparkConf就是你的操作面板。

　　SparkConf的构造很简单，主要通过<font color="red">ConcurrentHashMap</font>来维护各种Spark的配置属性。

~~~scala
/**
* Spark应用程序的配置。用于将各种Spark参数设置为键值对。
* 大多数情况下，您将使用“new SparkConf()”创建一个SparkConf对象，该对象还将从您的应用程序中设置的任何“ spark.*”Java系统属性中加载值。在这种情况下，直接在`SparkConf`对象上设置的参数优先于系统属性。
* 对于单元测试，无论系统属性是什么，您也可以调用`new SparkConf（false）`跳过加载外部设置并获得相同的配置。
* 此类中的所有setter方法都支持chaining编程。比如你可以写`new SparkConf().setMaster("local").setAppName("My app")`.
* @param loadDefaults 是否还从Java系统属性中加载值
* @note 一旦将SparkConf对象传递给Spark，它就会被克隆，并且用户无法再对其进行修改。 Spark不支持在运行时修改配置。
*/
class SparkConf(loadDefaults: Boolean) extends Cloneable with Logging with Serializable {

  import SparkConf._
  /** Create a SparkConf that loads defaults from system properties and the classpath */
  def this() = this(true)
  private val settings = new ConcurrentHashMap[String, String]()
    
  if (loadDefaults) {
    loadFromSystemProperties(false)
  }

  private[spark] def loadFromSystemProperties(silent: Boolean): SparkConf = {
    // Load any spark.* system properties 加载任何以spark开头的系统属性
    for ((key, value) <- Utils.getSystemProperties if key.startsWith("spark.")) {
      set(key, value, silent)
    }
    this
  }
~~~

SparkContext的主构造器参数为SparkConf，其实现如下：

~~~scala
/**
* Spark功能的主要入口点。 SparkContext表示与Spark集群的连接，可用于在该集群上创建RDD，累加器和广播变量。
* @note 每个JVM应该只有一个SparkContext是活动的。在创建一个新的SparkContext之前，您必须stop之前的。
* @param 指定一个描述应用程序配置的Spark Config对象，此配置中的任何设置都将覆盖默认配置以及系统属性。
*/
class SparkContext(config: SparkConf) extends Logging {

  // 构造此SparkContext的呼叫站点。保存着线程栈中最靠近栈顶的用户定义的类和最靠近栈底的Scala或者Spark核心类的信息，其中ShortForm属性保存着上述信息的间断描述，LongForm属性保存着上述信息的完整描述
  private val creationSite: CallSite = Utils.getCallSite()

  // 为了防止同时激活多个SparkContext，请将此上下文标记为已开始构建。
  // NOTE: this must be placed at the beginning of the SparkContext constructor.
  SparkContext.markPartiallyConstructed(this)
~~~

markPartiallyConstructed用来确保实例的唯一性。必须指定`spark.master`和`spark.app.name`，否则抛出异常。

SparkContext内部组件：

~~~scala
//SparkContext的配置，会先调用config的clone方法，在进行验证配置，是否设置了spark.master和spark.app.name
private var _conf: SparkConf = _
//事件日志的路径,当spark.enabled属性为true时启用，默认为/tmp/spark-events,也可以通过spark.eventLog.dir来指定目录
private var _eventLogDir: Option[URI] = None
//事件日志的压缩算法，当spark.eventLog.enabled属性与spark.eventLog.compress属性为true时，压缩算法默认为lz4。也可以通过spark.io.compression.codec属性指定，目前支持lzf,snappy和lz4
private var _eventLogCodec: Option[String] = None
//SparkContext中的事件总线，可以接收各个使用者的事件，异步将SparkListenerevent传递给注册的SparkListener
private var _listenerBus: LiveListenerBus = _
//Spark运行时环境，Spark 中任务执行是通过Executor，所有的Executor都有自己的执行环境SparkEnv，在Driver中也包含了SparkEnv，为了保证`Local模式的运行，SparkEnv内部还提供了不同的组件，来实现不同的功能
private var _env: SparkEnv = _
//用于监控作业和Stage进度状态的低级API
private var _statusTracker: SparkStatusTracker = _
//定期从sc.statusTracker获得active stage的状态信息，展示到进度条［在SparkUI上面可以看到进度条］，会有一定的延时。内部有一个timer，500ms refresh一遍
private var _progressBar: Option[ConsoleProgressBar] = None
//Spark的用户界面，SparkUI间接依赖于计算引擎，调度引擎，存储引擎，Job，Stage，Executor等组件的监控都会以SparkListenerEvent的形式传递给LiveListenerBus，SparkUI将从各个SparkListener中读取数据并显示在web界面
private var _ui: Option[SparkUI] = None
//Hadoop配置信息，如果系统属性SPARK_YARN_MODE为true或者环境变量SPARK_YARN_MODEL为true，那么将会是YARN的配置，否则为Hadoop的配置
private var _hadoopConfiguration: Configuration = _
//Executor内存大小，默认为1024MB，可以通过设置环境变量（SPARK_MEM或者SPARK_EXECUTOR_MEMORY）或者Spark.executor.memory属性指定其中Spark.executor.memory优先级最高
private var _executorMemory: Int = _
private var _schedulerBackend: SchedulerBackend = _
//Task调度器，是Spark调度系统中重要的组件之一，负责将任务发送到集群，运行。如果有失败的任务则重新执行，之后返回给DAGScheduler。TaskScheduler调度的Task是由DAGScheduler创建的，所以DAGScheduler是TaskScheduler前置调度。
private var _taskScheduler: TaskScheduler = _
private var _heartbeatReceiver: RpcEndpointRef = _
//DAG调度器，是Spark调度系统中重要的组件之一，负责创建Job，将DAG的RDD划分到不同的Stage，提交stage等，SparkUI中有关Job和Stage监控数据都来自DAGScheduer
@volatile private var _dagScheduler: DAGScheduler = _
//当前应用的标识，TaskScheduler启动后会创建应用标识，通过调取TaskScheduler的ApplicationId获取的
private var _applicationId: String = _
//当前应用尝试执行的标识，Spark Driver在执行时会多次尝试，每次尝试都会生成一个标识来代表应用尝试执行的身份
private var _applicationAttemptId: Option[String] = None
private var _eventLogger: Option[EventLoggingListener] = None
private var _driverLogger: Option[DriverLogger] = None
//Executor动态分配管理器，根据工作负载动态调整Executor数量，当在配置spark.dynamicAlloction.enabled属性为true的情况下，在非local模式下或者spark.dynamicAllcation.testing属性为true时启用
private var _executorAllocationManager: Option[ExecutorAllocationManager] = None
//异步清理RDD、shuffle和广播状态信息
private var _cleaner: Option[ContextCleaner] = None
//LiveListenerBus是否已经启动的标记
private var _listenerBusStarted: Boolean = false
//用户提交的jar文件，当选择部署模式为yarn时，_jars是由spark.jars属性指定的jar文件和spark.yarn.dist.jars属性指定的并集
private var _jars: Seq[String] = _
//用户设置的文件，可以根据Spark.file属性指定
private var _files: Seq[String] = _
//设置关闭钩子的管理器，可以给应用设置钩子，这样当JVM退出的时候就会执行清理工作
private var _shutdownHookRef: AnyRef = _
private var _statusStore: AppStatusStore = _
private var _heartbeater: Heartbeater = _
private var _resources: scala.collection.immutable.Map[String, ResourceInformation] = _
private var _shuffleDriverComponents: ShuffleDriverComponents = _

private[spark] def env: SparkEnv = _env
// 用于每个本地文件的URL与添加此文件到addedFiles时的时间戳之间的映射缓存
private[spark] val addedFiles = new ConcurrentHashMap[String, Long]().asScala
//用于每个本地Jar文件的URL与添加此文件到addedJars时的时间戳之间的映射缓存
private[spark] val addedJars = new ConcurrentHashMap[String, Long]().asScala
// Keeps track of all persisted RDDs 用于对所有持久化的RDD保持跟踪
private[spark] val persistentRdds = {
    val map: ConcurrentMap[Int, RDD[_]] = new MapMaker().weakValues().makeMap[Int, RDD[_]]()
    map.asScala
}
// 用于对所有持久化的RDD保持跟踪
private[spark] val executorEnvs = HashMap[String, String]()
//当前系统的登录用户，可以通过环境变量SPARK_USER来设置 通过
val sparkUser = Utils.getCurrentUserName()
//RDD计算过程中用于记录RDD检查点的目录
private[spark] var checkpointDir: Option[String] = None
//类型为AtomicInteger，用于生成下一个shuffle标识
private val nextShuffleId = new AtomicInteger(0)
//类型为atomicInteger，用于生成下一个rdd标识
private val nextRddId = new AtomicInteger(0)
~~~

~~~
allowMulitipleContext : 是否允许多个SparkContext实例，默认为False，可以通过设置Spark.Driver.allowMulitipleContexts来控制

startTime：标记sparkContext的启动时间戳

stopped：标记sparkContext是否处于停止状态，采用原子类型AtomicBoolean

HeatbeatReceiver：心跳接收器，所有的Executor都会向HeatbeatReceiver发送心跳信息，HeatbeatReceiver接收到心跳之后，先更新Executor最后可见时间，然后将此信息交给TaskScheduler。

localProperties：InheritableThreadLocal保护的线程，其中的属性值可以沿着线程栈一直传递下去。线程局部变量，用户可用于将信息向下传递到堆栈
~~~



~~~scala
try {
    // 1.初始化configuration
    _conf = config.clone()
    _conf.validateSettings()

    if (!_conf.contains("spark.master")) {
        throw new SparkException("A master URL must be set in your configuration")
    }
    if (!_conf.contains("spark.app.name")) {
        throw new SparkException("An application name must be set in your configuration")
    }

    _driverLogger = DriverLogger(_conf)

    val resourcesFileOpt = conf.get(DRIVER_RESOURCES_FILE)
    val allResources = getOrDiscoverAllResources(_conf, SPARK_DRIVER_PREFIX, resourcesFileOpt)
    _resources = {
        // driver submitted in client mode under Standalone may have conflicting resources with
        // other drivers/workers on this host. We should sync driver's resources info into
        // SPARK_RESOURCES/SPARK_RESOURCES_COORDINATE_DIR/ to avoid collision.
        if (isClientStandalone) {
            acquireResources(_conf, SPARK_DRIVER_PREFIX, allResources, Utils.getProcessId)
        } else {
            allResources
        }
    }
    logResourceInfo(SPARK_DRIVER_PREFIX, _resources)

    // log out spark.app.name in the Spark driver logs
    logInfo(s"Submitted application: $appName")

    // System property spark.yarn.app.id must be set if user code ran by AM on a YARN cluster
    if (master == "yarn" && deployMode == "cluster" && !_conf.contains("spark.yarn.app.id")) {
        throw new SparkException("Detected yarn cluster mode, but isn't running on a cluster. " +
                                 "Deployment to YARN is not supported directly by SparkContext. Please use spark-submit.")
    }

    if (_conf.getBoolean("spark.logConf", false)) {
        logInfo("Spark configuration:\n" + _conf.toDebugString)
    }

    // Set Spark driver host and port system properties. This explicitly sets the configuration
    // instead of relying on the default value of the config constant.
    _conf.set(DRIVER_HOST_ADDRESS, _conf.get(DRIVER_HOST_ADDRESS))
    _conf.setIfMissing(DRIVER_PORT, 0)

    _conf.set(EXECUTOR_ID, SparkContext.DRIVER_IDENTIFIER)

    _jars = Utils.getUserJars(_conf)
    _files = _conf.getOption(FILES.key).map(_.split(",")).map(_.filter(_.nonEmpty))
    .toSeq.flatten
	// 2.初始化日志目录并设置压缩类
    _eventLogDir =
    if (isEventLogEnabled) {
        val unresolvedDir = conf.get(EVENT_LOG_DIR).stripSuffix("/")
        Some(Utils.resolveURI(unresolvedDir))
    } else {
        None
    }

    _eventLogCodec = {
        val compress = _conf.get(EVENT_LOG_COMPRESS)
        if (compress && isEventLogEnabled) {
            Some(CompressionCodec.getCodecName(_conf)).map(CompressionCodec.getShortName)
        } else {
            None
        }
    }
    // 3.LiveListenerBus负责将SparkListenerEvent异步地传递给对应注册的SparkListener.
    _listenerBus = new LiveListenerBus(_conf)

    // Initialize the app status store and listener before SparkEnv is created so that it gets
    // all events.
    val appStatusSource = AppStatusSource.createSource(conf)
    // 4. 给 app 提供一个 kv store（in-memory）
    _statusStore = AppStatusStore.createLiveStore(conf, appStatusSource)
    // 5. 注册 AppStatusListener 到 LiveListenerBus 中
    listenerBus.addToStatusQueue(_statusStore.listener.get)

    // Create the Spark execution environment (cache, map output tracker, etc)
    // 6. 创建 driver端的 env
    // 包含所有的spark 实例运行时对象（master 或 worker），包含了序列化器，RPCEnv，block manager， map out tracker等等。
    // 当前的spark 通过一个全局的变量代码找到 SparkEnv，所有的线程可以访问同一个SparkEnv，
    // 创建SparkContext之后，可以通过 SparkEnv.get方法来访问它
    _env = createSparkEnv(_conf, isLocal, listenerBus)
    SparkEnv.set(_env)

    // If running the REPL, register the repl's output dir with the file server.
    _conf.getOption("spark.repl.class.outputDir").foreach { path =>
        val replUri = _env.rpcEnv.fileServer.addDirectory("/classes", new File(path))
        _conf.set("spark.repl.class.uri", replUri)
    }
    // 7. 从底层监控 spark job 和 stage 的状态并汇报的 API
    _statusTracker = new SparkStatusTracker(this, _statusStore)
	// 8. console 进度条
    _progressBar =
    if (_conf.get(UI_SHOW_CONSOLE_PROGRESS)) {
        Some(new ConsoleProgressBar(this))
    } else {
        None
    }
	// 9. spark ui, 使用jetty 实现
    _ui =
    if (conf.get(UI_ENABLED)) {
        Some(SparkUI.create(Some(this), _statusStore, _conf, _env.securityManager, appName, "",
                            startTime))
    } else {
        // For tests, do not enable the UI
        None
    }
    // Bind the UI before starting the task scheduler to communicate
    // the bound port to the cluster manager properly
    _ui.foreach(_.bind())
    
	// 10. 创建 hadoop configuration
    _hadoopConfiguration = SparkHadoopUtil.get.newConfiguration(_conf)
    // Performance optimization: this dummy call to .size() triggers eager evaluation of
    // Configuration's internal  `properties` field, guaranteeing that it will be computed and
    // cached before SessionState.newHadoopConf() uses `sc.hadoopConfiguration` to create
    // a new per-session Configuration. If `properties` has not been computed by that time
    // then each newly-created Configuration will perform its own expensive IO and XML
    // parsing to load configuration defaults and populate its own properties. By ensuring
    // that we've pre-computed the parent's properties, the child Configuration will simply
    // clone the parent's properties.
    _hadoopConfiguration.size()

    // 11. Add each JAR given through the constructor
    if (jars != null) {
        jars.foreach(addJar)
    }

    if (files != null) {
        files.foreach(addFile)
    }
	// 12.计算executor的内存
    _executorMemory = _conf.getOption(EXECUTOR_MEMORY.key)
    .orElse(Option(System.getenv("SPARK_EXECUTOR_MEMORY")))
    .orElse(Option(System.getenv("SPARK_MEM"))
            .map(warnSparkMem))
    .map(Utils.memoryStringToMb)
    .getOrElse(1024)

    // Convert java options to env vars as a work around
    // since we can't set env vars directly in sbt.
    for { (envKey, propKey) <- Seq(("SPARK_TESTING", IS_TESTING.key))
         value <- Option(System.getenv(envKey)).orElse(Option(System.getProperty(propKey)))} {
        executorEnvs(envKey) = value
    }
    Option(System.getenv("SPARK_PREPEND_CLASSES")).foreach { v =>
        executorEnvs("SPARK_PREPEND_CLASSES") = v
    }
    // The Mesos scheduler backend relies on this environment variable to set executor memory.
    // TODO: Set this only in the Mesos scheduler.
    executorEnvs("SPARK_EXECUTOR_MEMORY") = executorMemory + "m"
    executorEnvs ++= _conf.getExecutorEnv
    executorEnvs("SPARK_USER") = sparkUser

    _shuffleDriverComponents = ShuffleDataIOUtils.loadShuffleDataIO(config).driver()
    _shuffleDriverComponents.initializeApplication().asScala.foreach { case (k, v) =>
        _conf.set(ShuffleDataIOUtils.SHUFFLE_SPARK_CONF_PREFIX + k, v)
    }

    // We need to register "HeartbeatReceiver" before "createTaskScheduler" because Executor will
    // retrieve "HeartbeatReceiver" in the constructor. (SPARK-6640)
    // 13. 创建 HeartbeatReceiver endpoint
    _heartbeatReceiver = env.rpcEnv.setupEndpoint(
        HeartbeatReceiver.ENDPOINT_NAME, new HeartbeatReceiver(this))

    // Create and start the scheduler
    // 14. 创建 task scheduler 和 scheduler backend
    val (sched, ts) = SparkContext.createTaskScheduler(this, master, deployMode)
    _schedulerBackend = sched
    _taskScheduler = ts
    // 15. 创建DAGScheduler实例
    _dagScheduler = new DAGScheduler(this)
    _heartbeatReceiver.ask[Boolean](TaskSchedulerIsSet)

    // create and start the heartbeater for collecting memory metrics
    _heartbeater = new Heartbeater(
        () => SparkContext.this.reportHeartBeat(),
        "driver-heartbeater",
        conf.get(EXECUTOR_HEARTBEAT_INTERVAL))
    _heartbeater.start()

    // start TaskScheduler after taskScheduler sets DAGScheduler reference in DAGScheduler's
    // constructor
    // 16. 启动Taskscheduler
    _taskScheduler.start()
	// 17. 从Taskscheduler获取applicationId
    _applicationId = _taskScheduler.applicationId()
    // 18. 从Taskscheduler获取 application attempt id
    _applicationAttemptId = _taskScheduler.applicationAttemptId()
    _conf.set("spark.app.id", _applicationId)
    if (_conf.get(UI_REVERSE_PROXY)) {
        System.setProperty("spark.ui.proxyBase", "/proxy/" + _applicationId)
    }
    // 19.为ui设置applicationId
    _ui.foreach(_.setAppId(_applicationId))
    // 20.初始化block manager
    _env.blockManager.initialize(_applicationId)

    // The metrics system for Driver need to be set spark.app.id to app ID.
    // So it should start after we get app ID from the task scheduler and set spark.app.id.
    // 21.启动测量系统metricsSystem
    _env.metricsSystem.start()
    // 22.Attach the driver metrics servlet handler to the web ui after the metrics system is started.
    _env.metricsSystem.getServletHandlers.foreach(handler => ui.foreach(_.attachHandler(handler)))
	// 23.初始化event logger listener
    _eventLogger =
    if (isEventLogEnabled) {
        val logger =
        new EventLoggingListener(_applicationId, _applicationAttemptId, _eventLogDir.get,
                                 _conf, _hadoopConfiguration)
        logger.start()
        listenerBus.addToEventLogQueue(logger)
        Some(logger)
    } else {
        None
    }

    _cleaner =
    if (_conf.get(CLEANER_REFERENCE_TRACKING)) {
        Some(new ContextCleaner(this, _shuffleDriverComponents))
    } else {
        None
    }
    _cleaner.foreach(_.start())
	// 24.如果启用了动态分配 executor，需要实例化executorAllocationManager，并启动之
    val dynamicAllocationEnabled = Utils.isDynamicAllocationEnabled(_conf)
    _executorAllocationManager =
    if (dynamicAllocationEnabled) {
        schedulerBackend match {
            case b: ExecutorAllocationClient =>
            Some(new ExecutorAllocationManager(
                schedulerBackend.asInstanceOf[ExecutorAllocationClient], listenerBus, _conf,
                cleaner = cleaner))
            case _ =>
            None
        }
    } else {
        None
    }
    _executorAllocationManager.foreach(_.start())
	// 25. 建立并启动ListenerBus
    setupAndStartListenerBus()
    // 26. task scheduler 已就绪，发送环境已更新请求
    postEnvironmentUpdate()
    // 27. 发送 application start 请求事件
    postApplicationStart()

    // Post init
    // 28.等待,直至task scheduler backend 准备好了
    _taskScheduler.postStartHook() 
    // 29. 注册 dagScheduler metricsSource
    _env.metricsSystem.registerSource(_dagScheduler.metricsSource)
    _env.metricsSystem.registerSource(new BlockManagerSource(_env.blockManager))
    _env.metricsSystem.registerSource(new JVMCPUSource())
    _executorAllocationManager.foreach { e =>
        _env.metricsSystem.registerSource(e.executorAllocationManagerSource)
    }
    appStatusSource.foreach(_env.metricsSystem.registerSource(_))
    // Make sure the context is stopped if the user forgets about it. This avoids leaving
    // unfinished event logs around after the JVM exits cleanly. It doesn't help if the JVM
    // is killed, though.
    logDebug("Adding shutdown hook") // force eager creation of logger
    // 30. 设置 shutdown hook， 在spark context 关闭时，要做的回调操作
    _shutdownHookRef = ShutdownHookManager.addShutdownHook(
        ShutdownHookManager.SPARK_CONTEXT_SHUTDOWN_PRIORITY) { () =>
        logInfo("Invoking stop() from shutdown hook")
        try {
            stop()
        } catch {
            case e: Throwable =>
            logWarning("Ignoring Exception while stopping SparkContext from shutdown hook", e)
        }
    }
} catch {
    case NonFatal(e) =>
    logError("Error initializing SparkContext.", e)
    try {
        stop()
    } catch {
        case NonFatal(inner) =>
        logError("Error stopping SparkContext after init error.", inner)
    } finally {
        throw e
    }
}
~~~

SparkContext的初始化步骤如下：

1. 创建Spark执行环境SparkEnv

2. ~~创建RDD清理器metadataCleaner~~

3. 创建并初始化SparkUI

4. Hadoop相关配置及Executor环境变量的设置

5. 创建TaskScheduler(SparkLauncher，LauncherServer，LauncherBackend的通信流程)
6. 创建和启动DAGScheduler
7. TaskScheduler的启动
8. 初始化BlockManager
9. 启动测量系统MetricsSystem
10. 创建和启动Executor分配管理器ExecutorAllocationManager
11. ContextCleaner的创建和启动
12. Spark环境更新
13. 创建DAGSchedulerSource和BlockManagerSource
14. 将SparkContext标记为激活

## 二、创建Spark执行环境SparkEnv

~~~scala
// 这个函数允许在单元测试中模拟（mock）由SparkEnv创建的组件：
private[spark] def createSparkEnv(
    conf: SparkConf,
    isLocal: Boolean,
    listenerBus: LiveListenerBus): SparkEnv = {
    SparkEnv.createDriverEnv(conf, isLocal, listenerBus,SparkContext.numDriverCores(master, conf))
}
~~~

