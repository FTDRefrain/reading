## jQuery相关

1. 单击显示目录下的内容代码

   ```javascript
   $('.level1 > a').click(function(){
     // 点击的a添加class，子元素显示
     $(this).addClass('current').next().show()
     	// 回到上级找到同辈，将下面的a有激活的就去除
       .parent().siblings().children('a').removeClass('current')
     	// 子元素都隐藏
       .next().hide();
     return false
   })
   ```

2. 匿名函数和形参

   ```javascript
   (function($){
     var bar = function(){};
     $.bar = bar
   })(jQuery)
   ```

3. `font-weight: bold`字体加粗

4. `$('a')`选择器不存在也不会报错

5. `<input type="checkbox" name="1">`绑定相同的name实现了多选框

6. 挑选元素

   ```javascript
   // index大于5且不是最后一个
   $('ul li:gt(:5):not(:last)')
   ```

7. `$(html)`工厂模式，构建jQuery对象

8. 下拉多选

   ```jsx
   <select>
   	<option></option>
   </select>
   <select multiple="multiple">
   	<option></option>
   </select>
   ```

9. children表示的是子元素不是后代元素

10. a标签带有title属性的时候，会默认提示，但是速度慢

11. checked和selected控制单选和多选值；

12. 利用类名字添加后缀实现钩子，根据钩子能找到特定的位置；

13. 自动执行配合特定hover：设置internal，事件触发则删除internal；