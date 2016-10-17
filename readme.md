# logic-puzzle
### 一期源码逻辑题流程

##### 1. 点击逻辑题后发生了什么？
    1. 注册登录后，在 dashboard.html 页面，点击逻辑题<a>标签直接链接到 start.html?sectionId=1 页面，点击开始按钮，链接到 logic-puzzle.html?sectonId=1 页面。 

##### 2. 点击上一题发生了什么？

    1. 发送请求到 https://localhost:8888/api/logic-puzzle/save  （POST，参数：{userAnswer:_answer, orderId: _currentIndex} ） 保存当前题目的答案。	 	
      响应为:  OK  或 INTERNAL_SERNAL_ERROR

    2. 发送请求到 https://localhost:8888/api/logic-puzzle?orderId=1  (GET，参数：orderId)  请求上一题的题目。
   
    3. 每 2min 发送一次请求 https://localhost:8888/api/logic-puzzle/remain-time?sectionId=1  （GET）在 store 中暂存时间。

##### 3. 点击下一题发生了什么？
    1. 发送请求到 https://localhost:8888/api/logic-puzzle/save    （POST） 保存当前题目的答案。		响应为200（OK）或 INTERNAL_SERNAL_ERROR
	
    2. 发送请求到 https://localhost:8888/api/logic-puzzle?orderId=1  (GET，参数：orderId)  请求上一题的题目。
      
    3. 每 2min 发送一次请求 https://localhost:8888/api/logic-puzzle/remain-time?sectionId=1  （GET）在 store 中暂存时间。

##### 4. 点击提交发生了什么？
	1. 在 onSubmitPaper 函数中向 /api/logic-puzzle 发请求，参数：{sectionId:sectionId}
	
    2. 服务器响应 OK，且 page(‘dashbord.html’)  

##### 5. 超时自动提交发生了什么？
	1. 当 remain-time <= 0 时，调用交卷按钮事件对应的函数。

##### 6. 计时是如何设置的？
	1. 初始时间设置为 5400s
	
	2. 每隔 1s 页面中的数字变化一次
	
	3. 每隔 2min 向 /api/logic-puzzle/remain-time?sectionId=1 发一次请求
	
	4. 为什么是 2min？
	    
	    JS 是单线程，项目中倒计时是 setTimeout() 方法，如果前台过程阻塞，倒计时也会被阻塞，在前台请求时间是不可靠的，因为可以人为修改，所以需要向后台请求剩余时间。
	    
	    设计的时候考虑到，如果有人知道原理是可以修改答题时间的，所以要设置一个不长不短的时间来保证剩余时间的相对准确性。现在可以理解为是一拍脑袋想出来的。




