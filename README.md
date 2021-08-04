## 日志库, 依赖conf库
	import "github.com/indeeda-tech/conf"

### a. 配置文件需要配置如下字段
 	#conf.ini
	
	app_name = "ads-svc"
	
	run_mode = "dev"
	
	log_path = "/data/indeeda/logs/ads-svc.log"
	
	sentry_dsn = ""


### b. 默认dev模式下, 错误日志不上报sentry, 如果sentry配置项为空, 也不上报

### c. 错误输出支持如下四个级别, prod模式, 不记录Debug日志
		tlog.Debug(format, ...)
		tlog.Info(format, ...)
		tlog.Warn(format, ...)
		tlog.Error(format, ...)

### d. 使用方法如下
	
    val, err := strconv.Atoi("a") 
	
	errMsg := tlog.Error("convert err (%v)", err)
	
	logErr := tlog.NewLogError(err, errconst.TOKEN_API_ERROR_DATABASE, errMsg)    // 构造LogError
	// logErr := tlog.NewRawLogError(err, errMsg)    // 构造LogError
	
	errMsgEx := fmt.Printf("uppder err (%v).", logErr.Error())
	
	logErr.AttachErrMsg(errMsgEx)   // 为LogError添加额外错误信息
	
	logErr.AttachRequest(req)       // 为LogError添加Request请求信息(Sentry使用)
	
	tlog.AsyncSend(logErr)          // 上报错误信息到Sentry
	
### e. 本地日志存储, 默认每个小时或者超过指定大小会截断, 同时定期会清理过期的日志文件

### f. 常用函数说明

    1. func NewLogError(err error, code, msg string)*LogError
   		支持添加_err_code信息 
    	使用场景: 通过_err_code, 映射到http的status_code


    2. func NewRawLogError(err error, msg string) *LogError
    	使用场景: 单纯作为错误日志的记录

	3. func (this *LogError) Error() error
		返回error信息
		
	4. func (this *LogError) ErrCode() string
		返回err_code信息
	
	5. func (this *LogError) ErrMsg() []string
		返回err_msg信息
