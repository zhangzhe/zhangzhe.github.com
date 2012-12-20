---
layout: post
title: "关于预防跨站攻击"
description: ""
category: DesignPattern
tags: [Rails, csrf, 安全]
---
{% include JB/setup %}

公司网站自从上周开始使用` exception_notification` 实现错误报警。截止到今天（2012.5.29），user_registrations controller  的 create action 共上报了1098个错误。虽然错误信息是所谓的 ‘undefined method [] for nil:NilClass ‘，但代码排查后我们发现，这是一个典型的漏洞攻击。

攻击者伪造了`name、company_name、nickname、password_confirmation、user_type、company_type、password、mobile_phone、email` 共9个属性，填写后越过Ajax验证提交表单。由于还缺少几个关键属性，虽提交成功，却没能在系统中生成记录。因为数据不足产生错误，从而最终被我们作为出错信息记录了下来。


虽然这次攻击没有造成严重后果，但也暴漏了材价的一个安全隐患：
**伪造好对应属性，只需要一段简单的curl类脚本，就可以无限制地在系统中增加用户记录。**


##对付这类攻击有以下几种常规解决方案：
- 增加注册码输入: 增加注册码的方法被很多网站使用，效果不错，维翰和书祥的单点登陆也使用了这个方案。
- IP异常管理，如封闭连续注册超过一定数量的独立IP: 作为另一种思路，理论上可以很好的解决上面的攻击。可作为补充方案。
- 使用Rails防止跨站攻击的技术 [protect_from_forgery](http://api.rubyonrails.org/classes/ActionController/RequestForgeryProtection/ClassMethods.html#method-i-protect_from_forgery): 根据过往的经验来说，这是解决上述问题的最快、最简单的方案。然而在使用中发现，protect_from_forgery不好用了：虽然会在后台验证AuthenticityToken，但即使验证不通过，程序依然会继续运行（3.0.4甚至不会提示WARNING）。



**经过google和实验，结果如下：**


    protect_from_forgery 作为防止跨站攻击的有效方案，在 Rails 3.0.4中做了升级，以更好的
	防御跨站攻击。升级后与之前的行为有很大的不同：protect_from_forgery遇到异常时不会再爆出
	错误，而只是提示一个警告并清空 session。因此已无法再防御上述的粗暴型注册脚本攻击。

最终我们通过覆写handle_unverified_request方法找回以前的功能:


{% highlight ruby %}
def handle_unverified_request
  reset_session
  raise ActionController::InvalidAuthenticityToken # or other strategy
end
{% endhighlight %}


<br>

结论：上面只是用到了protect_from_forgery的附属作用解决了我们的问题，关于 protect_from_forgery 的真正牛逼用途请看下面关于Authenticity Token的解释和[Rails guides](http://guides.rubyonrails.org/security.html#cross-site-request-forgery-csrf)。


在寻找解决方案的过程中我们做了一系列调研和实验，感兴趣的同学往下看：）

<br><br>

***

<br><br>

注：跨站攻击(Cross-Site Request Forgery)和protect_from_forgery在Rails API中的描述：

    Controller actions are protected from Cross-Site Request Forgery (CSRF) attacks
	by including a token in the rendered html for your application. This token is 
	stored as a random string in the session, to which an attacker does not have 
	access. When a request reaches your application, Rails verifies the received 
	token with the token in the session. Only HTML and JavaScript requests are 
	checked, so this will not protect your XML API (presumably you’ll have a 
	different authentication scheme there anyway). Also, GET requests are not 
	protected as these should be idempotent.

    CSRF protection is turned on with the protect_from_forgery method, which 
	checks the token and resets the session if it doesn’t match what was expected. 
	A call to this method is generated for new Rails applications by default. 
	You can customize the error message by editing public/422.html.


***
<br>
#1. 排查过程
发现被攻击，我们第一感觉要在application_controller加入protect_from_forgery。检查代码，发现protect_from_forgery赫然已经加入了。

尝试curl命令，发现可以越过authentauthenticity_token的验证直接进入controller代码，并造成 500 错误。

curl的实例：

    curl -d "authentauthenticity_token=ZKbyMUhu8NEUW87cWij4Gjlr2JzMkUVLjTpUEeyS41E=
	&user[login]=xxx&user[name]=ccc&user[email]=wwwwwww@ssss.sss&user[password]=
	1234123&user[password_confirmation]=1234123" http://xx.com/users



直觉上认定肯定是protect_from_forgery在代码的某处被覆盖了。但最终也没能找到。


无奈google，发现了这么两段：

    The documentation for the authenticity token/csrf behavior is out of date. 
	TheInvalidAuthenticityToken exception is no longer thrown in these cases, 
	instead your session is just reset. If you would like to handle this differently 
	(or the old way) you can define your own behavior inhandle_unverified_request 
	on your controller.

[http://stackoverflow.com/questions/6090063/rails-3-protect-from-forgery-not-working-correctly/6092858#6092858](http://stackoverflow.com/questions/6090063/rails-3-protect-from-forgery-not-working-correctly/6092858#6092858)


    The behavior was changed fairly recently but the documentation has yet to be 
	updated. The new approach being used is to presume the session has been 
	hijacked, and therefore to clear the session. Assuming your session contains 
	the all-important authentication information for this request (like the fact 
	you're logged in as alice) and your controller assures the user is 
	authenticated for this action, your request will be redirected to a login 
	page (or however you choose to handle a non logged-in user). However, for 
	requests which are not authenticated, like a sign-up form, the request would 
	go through using an empty session.

    It seems this commit also goes on to close a CSRF vulnerability, but I didn't 
	read into the details of that.

    To obtain the old behavior, you would simply define this method:
<br>

{% highlight ruby %}

def handle_unverified_request
  raise(ActionController::InvalidAuthenticityToken)
end

{% endhighlight %}

[http://stackoverflow.com/questions/5000333/how-does-rails-csrf-protection-work](http://stackoverflow.com/questions/5000333/how-does-rails-csrf-protection-work)

<br>


于是去查最新的 [rails guides](http://guides.rubyonrails.org/security.html#cross-site-request-forgery-csrf)

    blablabla ......
    This will automatically include a security token, calculated from the current 
	session and the server-side secret, in all forms and Ajax requests generated 
	by Rails. You won’t need the secret, if you use CookieStorage as session 
	storage. If the security token doesn’t match what was expected, the session 
	will be reset. Note: In Rails versions prior to 3.0.4, this raised an 
	ActionController::InvalidAuthenticityToken error.



**查 Rails 3.0.4 的 release note，追踪到：**

    There are two major changes in this fix, the behaviour when CSRFprotection 
	fails has changed and the token will now be required forall non-GET requests.
    After applying this patch failed CSRF requests will no longer generateHTTP 
	500 errors, instead the session will be reset. Users can override this 
	behaviour by overriding handle_unverified_request in their own controllers.

[http://weblog.rubyonrails.org/2011/2/8/csrf-protection-bypass-in-ruby-on-rails/](http://weblog.rubyonrails.org/2011/2/8/csrf-protection-bypass-in-ruby-on-rails/)


**于是找到3.0.4的代码更新：**

    Change the CSRF whitelisting to only apply to get requests.
	Unfortunately the previous method of browser detection and XHR whitelisting 
	is unable to prevent requests issued from some Flash animations and Java 
	applets.  To ease the work required to include the CSRF token in ajax requests 
	rails now supports providing the token in a custom http header: X-CSRF-Token: …
    This fixes CVE-2011-0447


![Alt text](/images/code_update_CSRF.jpg "code update CSRF")

[https://github.com/rails/rails/commit/ae19e4141f27f80013c11e8b1da68e5c52c779ea](https://github.com/rails/rails/commit/ae19e4141f27f80013c11e8b1da68e5c52c779ea)

**最新版本的 protect_from_forgery实现:**

{% highlight ruby %}

def protect_from_forgery(options = {})
  self.request_forgery_protection_token ||= :authenticity_token
  before_filter :verify_authenticity_token, options
end

protected
 # The actual before_filter that is used. Modify this to change how you handle unverified requests.
def verify_authenticity_token
  verified_request? || handle_unverified_request
end

def handle_unverified_request
  reset_session
end

{% endhighlight %}



发现正如guide里描述的，遇到攻击只是reset session而不爆出错。
<br>
**最新的api如下:**

![Alt text](/images/api.jpg "code update CSRF")

[http://api.rubyonrails.org/classes/ActionController/RequestForgeryProtection/ClassMethods.html#method-i-protect_from_forgery](http://api.rubyonrails.org/classes/ActionController/RequestForgeryProtection/ClassMethods.html#method-i-protect_from_forgery)
<br>


***
<br>
#2. 结论：protect_from_forgery的版本遇到异常时不会再爆出错误，而只是提示一个警告并清空 session。


当然可以通过patch的方式找回以前的功能：

{% highlight ruby %}
def handle_unverified_request    
  reset_session
  raise ActionController::InvalidAuthenticityToken
end
{% endhighlight %}

<br>

这里还有一个小插曲，ActionController::InvalidAuthenticityToken会和subdomain有兼容性问题，不知道这是不是 3.0.4 去掉 ActionController::InvalidAuthenticityToken的原因。当然这个问题可以通过配置文件解决。

***

<br>
#3. 关于Rails升级的一点思考
3.0.4这次升级可能会造成的问题是：旧的Rails程序可能会依赖 protect_from_forgery 解决上述攻击(粗暴型注册脚本攻击)。一旦旧的程序升级到Rails
新版本时可能会忽略这个问题，从而造成了潜在安全漏洞。

正是基于这个想法，我测试了包括 github 在内的12个Rails站点，结果在4个上面都发现了同样的隐患（当然另外8个肯定不乏基于Rails2的）。
其中github的处理方式很好，它给出的错误提示为：

    Usually this means that your user authenticity token is incorrect.
    This can happen when you visit a page with a form, then login in a
    separate window, and then return to resubmit the form.
    Here's what you can try next:

    Go back and refresh the page that brought you here. Make sure the
    toolbar shows that you're logged in.

    If the problem persists,
    <a href="http://github.com/contact">contact us</a>
    and we'll help get you on your way.

***


<br>
后话：

这里只是用到了protect_from_forgery的附属作用解决了我们的问题，关于 protect_from_forgery 的真正牛逼用途请看下面的解释和 [Rails guides](http://guides.rubyonrails.org/security.html#cross-site-request-forgery-csrf )。


    What happens: When the user views a form to create, update, or destroy 
	a resource, the rails app would create a random authenticity_token, 
	store this token in the session, and place it in a hidden field in the 
	form. When the user submits the form, rails would look for the 
	authenticity_token, compare it to the one stored in the session, and if 
	they match the request is allowed to continue.

    Why this happens: Since the authenticity token is stored in the session, 
	the client can not know its value. This prevents people from submitting 
	forms to a rails app without viewing the form within that app itself. 
	Imagine that you are using service A, you logged into the service and 
	everything is ok. Now imagine that you went to use service B, and you saw 
	a picture you like, and pressed on the picture to view a larger size of 
	it. Now, if some evil code was there at service B, it might send a request 
	to service A (which you are logged into), and ask to delete your account, 
	by sending a request tohttp://serviceA.com/close_account. This is what is 
	known as CSRF (Cross Site Request Forgery).

    If service A is using authenticity tokens, this attack vector is no longer 
	applicable, since the request from service B would not contain the correct 
	authenticity token, and will not be allowed to continue.

    Notes: Keep in mind, rails only checks POST, PUT, and DELETE requests. 
	GET request are not checked for authenticity token. Why? because the HTTP 
	specification states that GET requests should NOT create, alter, or destroy 
	resources at the server, and the request should be idempotent 
	(if you run the same command multiple times, you should get the same result 
	every time).

    Lessons: Use authenticity_token to protect your POST, PUT, and DELETE 
	requests. Also make sure not to make any GET requests that could potentially 
	modify resources on the server.


[Understand Rails Authenticity Token!](http://stackoverflow.com/questions/941594/understand-rails-authenticity-token)
<br>
