### hexo的gitalk设置

1. 在所有页面均出现,在当前使用的theme->layout->comments.swig下添加下列代码块

   ```java
   {% elseif theme.gitalk.enable %}
   	<div class="comments" id="comments">
         <div id="gitalk-container"></div>
     </div>
   ```

   

2. 只在自己自己提交的博客页面出现，剔除1，然后再当前使用的theme->layout->_macro->post.swig末尾标签内填入下列代码

   ```java
   <div>
           {% if not is_index and theme.gitalk.enable %}
           {% include 'gitalk.swig' %}
           {% endif %}
       </div>
   ```

   

3. 指定在某些系统页面诸如about、tag、category等页面出现，在再当前使用的theme->layout->page.swig页面添加代码块

   ```java
   {% elif page.type === 'about' %}
   			{{ page.content }}
   			<div>
   				{% if not is_index and theme.gitalk.enable %}
   					{% include 'gitalk.swig' %}
   				{% endif %}
   			</div>
   ```

   