###使用说明：
modefy by ngx_lua_waf
在/etc/nginx/nginx.conf的http段添加
    lua_package_path "/etc/nginx/wafconf/?.lua";
    lua_shared_dict limit 10m;
    init_by_lua_file  /etc/nginx/wafconf/init.lua;
    access_by_lua_file /etc/nginx/wafconf/waf.lua;

配置config.lua里的waf规则目录(一般在waf/conf/目录下)
        RulePath = "/etc/nginx/wafconf/wafconf/"
绝对路径如有变动，需对应修改
然后重启nginx即可 service nginx restart
###配置文件详细说明：
    	RulePath = "/etc/nginx/wafconf/wafconf/"
        --规则存放目录
        attacklog = "off"
        --是否开启攻击信息记录，需要配置logdir
        logdir = "/usr/local/nginx/logs/hack/"
        --log存储目录，该目录需要用户自己新建，切需要nginx用户的可写权限
        UrlDeny="on"
        --是否拦截url访问
        Redirect="on"
        --是否拦截后重定向
        CookieMatch = "on"
        --是否拦截cookie攻击
        postMatch = "on" 
        --是否拦截post攻击
        whiteModule = "on" 
        --是否开启URL白名单
        ipWhitelist={"127.0.0.1"}
        --ip白名单，多个ip用逗号分隔
        ipBlocklist={"1.0.0.1"}
        --ip黑名单，多个ip用逗号分隔
        CCDeny="on"
        --是否开启拦截cc攻击(需要nginx.conf的http段增加lua_shared_dict limit 10m;)
        CCrate = "100/60"
        --设置cc攻击频率，单位为秒.
        --默认1分钟同一个IP只能请求同一个地址100次
        html=[[Please go away~~]]
        --警告内容,可在中括号内自定义
        备注:不要乱动双引号，区分大小写
###检查规则是否生效
部署完毕可以尝试如下命令：        
        curl http://xxxx/test.php?id=../etc/passwd
        返回"Please go away~~"字样，说明规则生效。
注意:默认，本机在白名单不过滤，可自行调整config.lua配置
	过滤规则在wafconf下，可根据需求自行调整，每条规则需换行,或者用|分割
		global是全局过滤文件，里面的规则对post和get都过滤		
		get是只在get请求过滤的规则		
		post是只在post请求过滤的规则		
		whitelist是白名单，里面的url匹配到不做过滤		
		user-agent是对user-agent的过滤规则
	默认开启了get和post过滤，需要开启cookie过滤的，编辑waf.lua取消部分--注释即可
