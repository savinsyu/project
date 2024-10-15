*Read this in other languages: [Russian](readme.ru.md)*

# File generation requirements
## Saving dependencies
```
python -m pip freeze > requirements.txt
```
## Running dependencies from a file
```
pip install -r requirements.txt
```
# Creating tables 
```
CREATE TABLE
  IF NOT EXISTS tasks (
    id_task INTEGER PRIMARY KEY AUTOINCREMENT,
    datetime_task datetime default CURRENT_TIMESTAMP,
    title_task TEXT NOT NULL,
    content TEXT NOT NULL,
    status_task BOOLEAN
  )

CREATE TABLE
  IF NOT EXISTS posts (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    content TEXT NOT NULL
  )
  
create table sandbox
(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    content  text
);  


CREATE TABLE
  IF NOT EXISTS git_and_bash (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    command TEXT NOT NULL,
    name TEXT NOT NULL
  )
  
```
# Creating routes
Creating routesTo create a page, a link to it is written in the base or navbar template
Here is an example of a link:
```
<a class="nav-link" href="{{ url_for('sql') }}"> SQL </a>
```
In the application script itself, in our git case app.py, we prescribe the route to the page.
```
@app.route('/sql')
def sql():
    return render_template('sql.html')
```
Where the 1st line is the route itself, the 2nd line is the function declaration and the 3rd line is the page output from the templates.

The page itself can be simply copied from index.html to change the content.
```
{% extends "base.html" %}
{% block title %}Home Page{% endblock %}
{% block content %}
<p>Главная</p>
{% endblock %}
```
# Uploading pictures
HTML page.
```
<title>Python Flask File Upload Example</title>
<h2>Select a file to upload</h4>
<p>
	{% with messages = get_flashed_messages() %}
	  {% if messages %}
		<ul class=flashes>
		{% for message in messages %}
		  <li>{{ message }}</li>
		{% endfor %}
		</ul>
	  {% endif %}
	{% endwith %}
</p>
<form method="post" action="/" enctype="multipart/form-data">
    <dl>
		<p>
			<input type="file" name="file" autocomplete="off" required>
		</p>
    </dl>
    <p>
		<input type="submit" value="Submit">
	</p>
</form>
```
App logic.
The most important thing is to correctly specify the folder where the files will be saved
```
UPLOAD_FOLDER = 'static'
# расширения файлов, которые разрешено загружать
ALLOWED_EXTENSIONS = {'txt', 'pdf', 'png', 'jpg', 'jpeg', 'gif'}

app = Flask(__name__)
# конфигурируем
app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER
bootstrap = Bootstrap5()
app.secret_key = "secret key"

@app.route('/upload_images')
def upload_images():
    return render_template("upload_images.html")

@app.route('/', methods=['POST'])
def upload_file():
    if request.method == 'POST':
        # check if the post request has the file part
        if 'file' not in request.files:
            flash('No file part')
            return redirect(request.url)
        file = request.files['file']
        if file.filename == '':
            flash('No file selected for uploading')
            return redirect(request.url)
        if file and allowed_file(file.filename):
            filename = secure_filename(file.filename)
            file.save(os.path.join(app.config['UPLOAD_FOLDER'], filename))
            flash('File successfully uploaded')
            return redirect('/')
        else:
            flash('Allowed file types are txt, pdf, png, jpg, jpeg, gif, py, docx')
            return redirect(request.url)
```
# Own styles 
To connect your css file, you need to write the following code in base.html
```
<link href="{{ url_for('static', filename='styles.css') }}" rel="stylesheet">
```
# Drop-down menu
```
<li class="nav-item dropdown">
  <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-bs-toggle="dropdown"
    aria-expanded="false">
    Сервисы
  </a>
  <ul class="dropdown-menu" aria-labelledby="navbarDropdown">
    <li><a class="dropdown-item" href="{{ url_for('dump') }}">Дамп базы данных</a></li>
    <li><a class="dropdown-item" href="{{ url_for('xlsx') }}">Выгрузка таблиц баз данных в ексель</a></li>
  </ul>
</li>
```
# Handling 404 Error in Flask
It often happens that the user accidentally incorrectly specifies a link in the address bar. In order for the error to be displayed correctly, you need to register page 404.
```
@app.errorhandler(404)
def page_not_found(e):
    return render_template('404.html'), 404
```
404.html
```
{% extends "base.html" %}
{% block title %}Page Not Found{% endblock %}
{% block content %}
  <h1>Page Not Found</h1>
  <p>What you were looking for is just not there.
  <p><a href="{{ url_for('index') }}">go somewhere nice</a>
{% endblock %}
```
# Creating a database backup
Creating an application file dump.py
```
import sqlite3
import io

conn = sqlite3.connect('database.db')

# Open() function
with io.open('database_dump.sql', 'w') as p:
    # iterdump() function
    for line in conn.iterdump():
        p.write('%s\n' % line)
print(' Backup performed successfully!')
print(' Data Saved as backupdatabase_dump.sql')
conn.close()
```
In the bash or cmd of the project's working directory, write the command:
```
python dump.py
```
After that, a dump of the database is created in the working directory.
# Port work
If you need to work with other projects on the local machine, you must specify different ports:
http://127.0.0.1:82
http://127.0.0.1:81
Specifies the port in this line of the application file:
if name == "main":
app.run(debug=True, host='0.0.0.0', port=81)

# Bash scripts
Below described scripts are given as an example, it is necessary to take into account the placement of catalogs in the system
## Creating a database dump
```
venv/scripts/python.exe dump.py
```

## Copy sql scripts to the sql-scripts folder from the dbeaver workspace
```
cp ~/dbeaver-ce-24.0.4-win32.win32.x86_64/dbeaver/bases/work/Scripts/Flask.sql /p/s.savin/flask_project/sql-scripts
```

## Launch the application
```
venv/scripts/python.exe app.py
```

## The app opens in the default browser
```
venv/scripts/python.exe -m webbrowser http://127.0.0.1:82
```

## Creating and sending a commit
```
set text= "Reserve Copy: %date%   %time% "
git add .
git commit -m %text%
git push
```

## Copy the DBeaver configuration
```
cp -r ~/dbeaver-ce-24.0.4-win32.win32.x86_64/dbeaver/bases* /p/s.savin/Work/
```

## Running two bash scripts in sequence
```
./dump_database.sh && ./commit.sh
```




