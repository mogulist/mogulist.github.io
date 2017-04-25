---
title: Code Example
description: This page contains examples for writing code in markdown.
header: Code Syntax Example
---
To insert highlight code inside of a post, it's enough to use some specific tags, has directly described into the [Jekyll documentation](http://jekyllrb.com/docs/templates/#code-snippet-highlighting). In this way the code will be included into a ``.highlight`` CSS class and will be highlight according to the [syntax.scss](https://github.com/mojombo/tpw/blob/master/css/syntax.css) file. This is the standard style adopted by **Github** to highlight the code. 

And this is a HTML example, with a linenumber:
{% highlight html linenos %}

<html>
  <a href="example.com">Example</a>
</html>

{% endhighlight %}

javascript example:
{% highlight html linenos %}

 http.get(fullUri, (res) => {
    const statusCode = res.statusCode;
    const contentType = res.headers['content-type'];
    var responseBody = "";

    //console.log("Response from server started.");
    console.log(`Server Status: ${res.statusCode} `);
    console.log("Response Headers: %j", res.headers);

    res.setEncoding("UTF-8");

    res.once("data", function(chunk) {
        console.log(chunk);
    });

    res.on("data", function(chunk) {
        console.log(`--chunk-- ${chunk.length}`);
        responseBody += chunk;
    });

    res.on("end", function() {
        fs.writeFile("res.json", responseBody, function(err) {
            if (err) {
                throw err;
            }
            console.log("File Downloaded");
        });
    });
})
{% endhighlight %}

This is a CSS example:
{% highlight css linenos %}

body {
  background-color: #fff;
  }

h1 {
  color: #ffaa33;
  font-size: 1.5em;
  }

{% endhighlight %}



Last, a Ruby example:
{% highlight ruby linenos %}

def hello
  puts "Hello World!"
end

{% endhighlight %}

Definition
: this is definition

```css
CSS Syntax Highlighting
#button {
    border: none;
}
```

~~~~
This is
a piece of
code block
~~~~

`var i` is a variable. `forEach` is useful.

```javascript
http.get(fullUri, (res) => {
    const statusCode = res.statusCode;
    const contentType = res.headers['content-type'];
    var responseBody = "";

    //console.log("Response from server started.");
    console.log(`Server Status: ${res.statusCode} `);
    console.log("Response Headers: %j", res.headers);

    res.setEncoding("UTF-8");

    res.once("data", function(chunk) {
        console.log(chunk);
    });

    res.on("data", function(chunk) {
        console.log(`--chunk-- ${chunk.length}`);
        responseBody += chunk;
    });

    res.on("end", function() {
        fs.writeFile("res.json", responseBody, function(err) {
            if (err) {
                throw err;
            }
            console.log("File Downloaded");
        });
    });
```