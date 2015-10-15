Title:  微信页面整合WebApi
SubTitle: 
Author: kk
Date: 2015年9月20日
Tags:   开发文档
        api
        微信


[TOC]
#微信页面整合WebApi

###域名：`http://xzzx2015.ncuhome.cn/`

请求的`Content-Type`为`application/json`。

返回数据为JSON格式：

	{
		success:true,
		data:object,
		error:{
			message:"error message",
			detail:"error detail"
		}
	}

其中`success`表示是否成功，`data`为请求成功返回的数据，请求失败时`data`为`null`。以下文档中请求成功返回数据示例均为`data`，省略了`success`和`error`；请求失败返回数据均为`error`。

**url不区分大小写，Json数据区分大小写。**

###登录流程

	if 第一次访问
		用账号密码登录
		if 登录成功
			保存guid
		else
			重新登录
	else
		用js在后台用guid登录
		if 登录成功
			保存guid
		else
			转到用账号密码登录


###jQuery请求示例
	
**data必须是JSON格式的**，jQuery默认生成的是表单格式的。

**contentType必须是application/json**，jQuery默认是application/x-www-form-urlencoded。

	$.ajax({
		url:"http://xzzx2015.ncuhome.cn/api/auth/loginbypassword",
		type:"Post",
		data:JSON.stringify({
			userName:"6102111234",
			password:"12345"}),
		contentType:"application/json",
		success:function(result,status,xhr){
			if (result.success) {
				//登录成功
				var data=result.data;
				var guid=data.guid;
				alert(guid);
			}else{
				//登录失败
				var error=data.error;
				var message=error.message;
				alert(message);
			}
		}
	})

					
用chrome插件Advanced Rest Client可以发送各种请求测试WebApi。

###家园网账号登陆

登陆成功后客户端保存guid，请求相应功能时需提供guid。

####1. 账号或学号+密码登陆: `POST api/Auth/LoginByPassword`

请求数据：

	{
		userName:"6102111234",
		password:"12345"
	}

请求成功返回：

	{
		userName: "6102111234"
		guid: "c478113d-2c64-41b9-ba92-c52b88acfecd"
		trueName: "真实姓名"
		schoolId: "6102111234"
	}

请求失败返回：

	{
		message: "登录失败：用户名或密码错误",
		detail: ""
	}

####2. Guid登陆: `POST api/Auth/LoginByGuid`

请求数据：

	{
		guid:"c478113d-2c64-41b9-ba92-c52b88acfecd"
	}

请求成功返回：

	{
		userName: "6102111234"
		guid: "c478113d-2c64-41b9-ba92-c52b88acfecd"
		trueName: "真实姓名"
		schoolId: "6102111234"
	}

请求失败返回：

	{
		message: "登录失败：Guid无效",
		detail: ""
	}


###奖学金信息

目前只能查询到最近一学期的加减分信息，查询其他学期的加减分信息时返回是还是本学期的数据。

获取个人奖学金信息只能查询到本学期的奖学金和加减分。

####1.奖学金学期: `POST api/JXJ/JXJTerms`

请求数据：

	{
		guid:"c478113d-2c64-41b9-ba92-c52b88acfecd"
	}

请求成功返回：

	 
	[
		{
			"Item1":2014,
			"Item2":2015,
			"Item3":1
		}
	]

请求失败返回：

	{
		message: "查询失败：Guid无效",
		detail: ""
	}


####2.个人奖学金: `POST api/JXJ/JXJInfo`

请求数据：

	{
		guid:"c478113d-2c64-41b9-ba92-c52b88acfecd"
	}

请求成功返回：

	 {
		"XH": "6102111234",
		"XQBJID": 18177,
		"XM": "真实姓名",
		"SHDF_ZF": 3,
		"HJXJQK": "无",
		"HJXJJE": 0,
		"PX_ZF": 14,
		"JJF": [
			{
				"GUID": 1625762,
				"DXLB": "思想道德素质",
				"JJFYY": "家园网部委",
				"JJFBZ": "加",
				"SHDF": 3
			},
			{
				"GUID": 1625766,
				"DXLB": "专业文化素质",
				"JJFYY": "",
				"JJFBZ": "加",
				"SHDF": 81.84
			}
		]
	}

请求失败返回：

	{
		message: "查询失败：Guid无效",
		detail: ""
	}


####3.班级奖学金: `POST api/JXJ/ClassJXJInfo`

请求数据：

	{
		guid:"c478113d-2c64-41b9-ba92-c52b88acfecd"，
		term:"2014-2015-1"
	}

请求成功返回：

	

请求失败返回：

	{
		message: "查询失败：Guid无效",
		detail:""
	}

###教务系统

客户端保存jSessionId，请求相应功能时需提供jSessionId。

####1.登录信息: `POST api/JWXT/LogInPage`

请求数据：无

请求成功返回：

	{
		success: true,
		data: {
			jSessionId: "C81D0FE05B85F9376065A469651F2772",
			needVerifyCode: true //是否需要验证码
		},
		error: null
	}

####2.验证码：`GET api/JWXT/VerifyCode?jSessionId={jSessionId}`

请求数据：jSessionId

请求成功返回：验证码图片

####3.登录: `POST api/JWXT/LogIn`

请求数据：

	{
		jSessionId:"4388C6DB23857DB9ECF8A2174E007672",
		userId:"6102111234",
		password:"em123456",
		verifyCode:"xyxc"
	}

请求成功返回：

	{
		message: "登录成功"
	}

请求失败返回：

	{
		message: "账号，密码或验证码错误"
		detail: ""
	}

####4.学期: `POST api/JWXT/MyTerms`

请求数据：

	{
		schoolId:"6102111234"
	}

请求成功返回：

	[{"m_Item1":2014,"m_Item2":2015,"m_Item3":2},
	{"m_Item1":2014,"m_Item2":2015,"m_Item3":1}]

####5.成绩: `POST api/JWXT/ScoreList`

请求数据：

	{
		jSessionId:"C81D0FE05B85F9376065A469651F2772",
		term:"2014-2015-2"
	}

请求成功返回：

	[
		{
			Score:"77",
			ScoreTag:"&nbsp;",
			CourseID:"J5510N0006",
			CourseName:"线性代数",
			CourseSemester:"2014-2015-2",
			CourseProperties:"学科基础课程",
			CourseCategory:"必修",
			CourseTime:"32",
			CourseScore:"2",
			ExamProperties:"正常",
			BuChongSemester:"&nbsp;"
		},
		...
	]

####6.课程表: `POST api/JWXT/CourseTable`

请求数据：

	{
		jSessionId:"C81D0FE05B85F9376065A469651F2772",
		term:"2014-2015-2"
	}

请求成功返回，其中Courses是一个5x7的二维数组：

	{
		Semester: "2014-2015-2",
		Courses: [
			[{
				Name: "数据结构",
				ClassName: "电子信息工程[141-142]班",
				Teacher: "刘小东",
				Time: " 1-19周",
				Address: "计算中心多媒体B312",
				Tag: 1
			},
			{
				Name: "高等数学(Ⅰ)下",
				ClassName: "计算机科学与技术[141-144]班,测控技术与仪器[141-142]班,新能源材料与器件141班,",
				Teacher: "樊树平",
				Time: " 1-16周",
				Address: "教223",
				Tag: 1
			},	
			...
			],
		...
		]
	}

Tag的值含义如下：

    Week = 1,
    SingleWeek = 2,//单周
    DoubleWeek = 3 //双周