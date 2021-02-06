# CI/CD PIPELINE USING GITHUB ACTIONS, LINUX AND PYTHON CODE APP

## Instruction set

The first step is create a repository on github:
https://github.com/new

Next, on your Linux shell environment desktop/laptop/server...

Open terminal and type this to create a virtual environment:
```bash
python3 -m venv myvenv
source myvenv/bin/activate
```

Install FLASK and pytest:
```bash
pip install flask pytest
```

Freeze all package requeriments into a file:
```bash
pip freeze > requirements.txt
```

Create directory skeleton:
```bash
mkdir {src,tests}
```

In src new directory create app.py:
```bash
cd src
touch app.py
```

Inside app.py put this:
```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def index():
  return "Hello world!"

if __name__ == "__main__":
  app.run()
```

And check:
```bash
python3 app.py
#  * Serving Flask app "app" (lazy loading)
#  * Environment: production
#    WARNING: This is a development server. Do not use it in a production deployment.
#    Use a production WSGI server instead.
#  * Debug mode: off
#  * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Now go to ../test
```bash
cd ../test
touch test_app.py
```

Inside of test_app.py put this:
```python
from app import index

def test_index():
  assert index() == "Hello world!"
```

Go back and export python path:
```bash
cd ..
export PYTHONPATH=src
```

Execute pytest:
```bash
pytest
```

Add .gitignore file:
```bash
cat <<EOF > .gitignore
myvenv/
__pycache__/
.pytest_cache/
.DS_Store
EOF
```

Check if only desired files will be uploaded:
```
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	.gitignore
	commands.md
	requirements.txt
	src/
	tests/
```

Add files where are untracked and commit changes:
```bash
git add -A
git commit -m "initial commit"
git push
```

Now, go to actions tab on github:

![github-actions-button](img/github-actions-button.png)

and click on:

![python-application](img/github-python-application.png)


Now, you go to check if all steps in a pipeline are successfully completed:


If any error occurs, you can troubleshoot them, don't pain and go here:
![github-build-status](img/github-build-status.png)

and check build status:

![github-build-result](img/github-build-result.png)

Oh no!, an error has occurred, fix this by adding the "exporting environment var" and continue:

edit --> .github/workflows/python-app.yml
![github-build-yaml-fix](img/github-build-yaml-fix.png)

Ok, all runs well now on workflow:
![github-build-test-ok](img/github-build-test-ok.png)

Looks great!!

Your test build finish successfully:
![github-build-successfully](img/github-build-successfully.png)


## Continuous Delivery

Now, we continue with automated delivery part of this tutorial, well, first sign up free with heroku by clicking on this link: https://signup.heroku.com/ 


### Install heroku

See instructions on web interface: https://devcenter.heroku.com/articles/heroku-cli

Well, I'm gonna be install heroku with my terminal like this:
```bash
# On Mac OS terminal:
brew tap heroku/brew && brew install heroku
```

***


#### Shell completion

Now you can put (only if you use oh-my-zsh shell) heroku in your plugins section (if you don't have oh-my-zsh, follow the instructions after heroku installation):

Edit ~/.zshrc
```bash
plugins=(git git-flow brew history kubectl ansible heroku)
```
***

Ok, when installation is complete, login with heroku on your terminal:
```bash
heroku login
````

### CREATE APP
Two options here:
  1. Create with the interface (very easy)
  2. More easy, automatically with your terminal like this:
```bash
heroku create
```
When is created click on the link.

