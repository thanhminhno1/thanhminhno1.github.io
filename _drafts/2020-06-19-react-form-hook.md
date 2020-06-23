---
layout: post
title: React Form Hook
excerpt: "Demo post displaying the various ways of highlighting code in Markdown."
tags: [sample post, code, highlighting]
modified: 2020-06-19
comments: true
---

Làm việc với form và một trong những vấn để thử thách nhất khi phát triển ứng dụng bằng React. React là một thư viện tối giản chỉ tập trung vào việc kiểm soát sự thay đổi của giao diện, đảm bảo răng giao diện người dùng sẽ thay đổi phù hợp với hoạt động người dùng. Nó không cung cấp một giải pháp hoàn chỉnh nào cho việc sử dụng input nhưng nó cung cho ta phương thức để lưu giá trị của input vào state.

Việc xử lý form với React yêu cầu bạn phải viết một số đoạn mã phục vụ việc:
1. Quản lý vào kiểm soát giá trị input mà bạn lưu vào state
2. Theo dõi error messages
3. xử lý submission

Để việc xử lý form được đơn giản hơn, nhiều developer đã tạo ra các thư viện giúp build form với React. Một trong những thư viện phổ biến nhất là [Formik](https://jaredpalmer.com/formik/) 

Bản thân tôi đã từng sử dụng thư viện này như một giải pháp khi làm việc với Form của React và nó hoạt động thực sự tuyệt vời, Nhưng gần đây tôi đang làm việc với một thư viện mới có tên là [React Hook Form](https://react-hook-form.com/)

Trong bài viết này tôi sẽ cho bạn thấy được lợi ích của việc sử dụng React Hook Form hơn là sử dụng Formik. Nhưng đầu tiên hãy tìm hiểu làm thế nào để tạo ra Form bằng React Form.

### How to handle forms with React

To modify styling and highlight colors edit `/_sass/_pygments.scss`.

{% highlight css %}
#container {
    float: left;
    margin: 0 -240px 0 0;
    width: 100%;
}
{% endhighlight %}

{% highlight html %}
{% raw %}
<nav class="pagination" role="navigation">
    {% if page.previous %}
        <a href="{{ site.url }}{{ page.previous.url }}" class="btn" title="{{ page.previous.title }}">Previous article</a>
    {% endif %}
    {% if page.next %}
        <a href="{{ site.url }}{{ page.next.url }}" class="btn" title="{{ page.next.title }}">Next article</a>
    {% endif %}
</nav><!-- /.pagination -->
{% endraw %}
{% endhighlight %}

{% highlight ruby %}
module Jekyll
  class TagIndex < Page
    def initialize(site, base, dir, tag)
      @site = site
      @base = base
      @dir = dir
      @name = 'index.html'
      self.process(@name)
      self.read_yaml(File.join(base, '_layouts'), 'tag_index.html')
      self.data['tag'] = tag
      tag_title_prefix = site.config['tag_title_prefix'] || 'Tagged: '
      tag_title_suffix = site.config['tag_title_suffix'] || '&#8211;'
      self.data['title'] = "#{tag_title_prefix}#{tag}"
      self.data['description'] = "An archive of posts tagged #{tag}."
    end
  end
end
{% endhighlight %}


### Standard Code Block

    {% raw %}
    <nav class="pagination" role="navigation">
        {% if page.previous %}
            <a href="{{ site.url }}{{ page.previous.url }}" class="btn" title="{{ page.previous.title }}">Previous article</a>
        {% endif %}
        {% if page.next %}
            <a href="{{ site.url }}{{ page.next.url }}" class="btn" title="{{ page.next.title }}">Next article</a>
        {% endif %}
    </nav><!-- /.pagination -->
    {% endraw %}


### Fenced Code Blocks

To modify styling and highlight colors edit `/_sass/_coderay.scss`. Line numbers and a few other things can be modified in `_config.yml`. Consult [Jekyll's documentation](http://jekyllrb.com/docs/configuration/) for more information.

~~~ css
#container {
    float: left;
    margin: 0 -240px 0 0;
    width: 100%;
}
~~~

~~~ html
{% raw %}<nav class="pagination" role="navigation">
    {% if page.previous %}
        <a href="{{ site.url }}{{ page.previous.url }}" class="btn" title="{{ page.previous.title }}">Previous article</a>
    {% endif %}
    {% if page.next %}
        <a href="{{ site.url }}{{ page.next.url }}" class="btn" title="{{ page.next.title }}">Next article</a>
    {% endif %}
</nav><!-- /.pagination -->{% endraw %}
~~~

~~~ ruby
module Jekyll
  class TagIndex < Page
    def initialize(site, base, dir, tag)
      @site = site
      @base = base
      @dir = dir
      @name = 'index.html'
      self.process(@name)
      self.read_yaml(File.join(base, '_layouts'), 'tag_index.html')
      self.data['tag'] = tag
      tag_title_prefix = site.config['tag_title_prefix'] || 'Tagged: '
      tag_title_suffix = site.config['tag_title_suffix'] || '&#8211;'
      self.data['title'] = "#{tag_title_prefix}#{tag}"
      self.data['description'] = "An archive of posts tagged #{tag}."
    end
  end
end
~~~

### GitHub Gist Embed

An example of a Gist embed below.

{% gist mmistakes/6589546 %}
