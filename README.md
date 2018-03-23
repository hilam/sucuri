<a href="#"><img src="https://user-images.githubusercontent.com/16294901/37826760-892cd0de-2e73-11e8-8ea1-2afc390c2ac0.png" height="300" align="right"></a>
# Sucuri
Simple and efficient template engine for Python projects inspired by [PugJS](https://pugjs.org)

## Instaling
Install and update using [pip](https://pip.pypa.io/en/stable/quickstart/):
> pip install sucuri

## Creating a Sucuri Template
- Example of code:
```
html
    body
        h1 Title
        a(href='#') This is my link
```
As can be seen in the code example above, the sucuri development requires tabulation standardization. We do not determine the number of spaces, but it is necessary to keep the same number of spaces on the left in the whole code, because this quantity will inform if a certain TAG of the HTML will be contained within another one or not. With this, in the example above we will have the following HTML code:
```
<html>
    <body>
        <h1>Title</h1>
        <a href="#">This is my link</a>
    </body>
</html>
```

## Using
To use sucuri, you need to import the sucuri package into your Python file, the example below is an application that uses the sucuri to render in the [Flask](http://flask.pocoo.org/):
```
from sucuri import rendering
from flask import Flask, render_template_string

app = Flask(__name__)

@app.route("/")
def index():
    template = rendering.template('template.suc')
    return render_template_string(template)
```

As can be seen in the example above, the template in the example is loaded from a file named `template.suc` which is in the project's root directory, however it could be in any project directory, such as `templates/template.suc` if you include a folder to group the templates.

### Text
In sucuri, texts are described in two ways. It can be written after the declaration of the tag such as:
```
h1 Title
```
Result:
```
<h1>Title</h1>
```
Or you can type in more than one line using the `|` on the lines that are not the same as the tag, see example:
```
h3 Hello!
    | Text
    | with
    | more than
    | one line
```
Result:
```
<h3>Hello!
    Text
    with
    more than
    one line
</h3>
```

### Rendering of data
We already know (seen in the text above) that we can only use the `template('template_name')` function with a simple `.suc` file, however it is possible to pass information through a JSON to the template and the sucuri will automatically render the data in the proper location, see the example below:
- Sucuri file:
```
html
    body
        h1 Hello {a}
            | Title
            | More
        a(href='#') This is my link
        h3 {b}
```
- Python example with data:
```
from flask import Flask, render_template_string
from sucuri import rendering

app = Flask(__name__)

@app.route("/")
def index():
    template = rendering.template('template_data.suc',{"a": 1, "b": "Hello!"})
    return render_template_string(template)
```
- Result:
```
<html>
    <body>
        <h1>Hello 1
        Title
        More
        </h1>
        <a href="#">This is my link</a>
        <h3>Hello!</h3>
    </body>
</html>
```

### Injecting template
Code reuse can be done through injected templates. This facility makes reuse of the code very efficient and enables the creation of code components. In the sucuri the identification of an injection occurs through an `include` at the beginning of the .suc file and its use is carried out using the `+` symbol before the name of the file that was imported. See the example below using this feature:
- Sucuri file (`template_include.suc`):
```
include inc/link
include inc/list

html
    body
        h1 Hello
            | Title
            | More
        +link
        h3 Oh Yeahh
        +list
```
- File inside the folder `inc` called `link.suc` (`inc/link.suc`):
```
a(href='#') {text}
```
- File inside the folder `inc` called `list.suc` (`inc/list.suc`):
```
ul
    li A
    li B
```
- Python example:
```
from flask import Flask, render_template_string
from sucuri import rendering

app = Flask(__name__)

@app.route("/")
def index():
    template = rendering.template('template_include.suc',{"text": "Hello! I'm here!"})
    return render_template_string(template)
```
- Result:
```
<html>
    <body>
        <h1>Hello
        Title
        More
        </h1>
        <a href="#">Hello! I'm here!</a>
        <h3>Oh Yeahh</h3>
        <ul>
            <li>A</li>
            <li>B</li>
        </ul>
    </body>
</html>
```
