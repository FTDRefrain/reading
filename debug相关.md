1. input监控onChange事件实现文件的上传而不是click事件
2. Base64, blob和file对象也可相互转换，且上传的时候，因为自己转换的file对象没有名字，所以需要将input拿到的file名字记录一下；上个对象可能在上传的时候有限制
3. 遇到返回的Id不对的问题或者内容不对类型为整型且后面总是多个0的时候，是因为JAVA和JS的int长度不同，所以js自动补全0
4. 记住http请求返回的json格式的东西，如果没有调用json()方式，本身会是包括所有内容的对象；
5. Object，embed或者iframe标签进行网页链接内容的展示，前两个有type，后一个没有；