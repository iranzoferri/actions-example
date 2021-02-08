# CI/CD PIPELINE :infinity:
### Using TERMINAL, PYTHON CODE APP and GITHUB ACTIONS deployed to HEROKU


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
pip install flask pytest gunicorn
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
Random app name is generated.
When is created click on the generated link.

Then, crate heroku authorization token:
```bash
heroku authorizations:create -d "Test token for github-actions-example"
# Creating OAuth Authorization... done
# Client:      <none>
# ID:          3a80d925-43ce-481c-8319-02208cf041fd
# Description: Test token for github-actions-example
# Scope:       global
# Token:       4b897ef5-e1f4-4b7f-988a-9cccac32b508
# Updated at:  Sat Feb 06 2021 17:49:19 GMT+0100 (GMT+01:00) (less than a minute ago)
```

Info: To delete this authorization, simply, use ID:
```bash
heroku authorizations:revoke 3a80d925-43ce-481c-8319-02208cf041fd
```

At this point, you need to go back to github to generate two secrets, one for the tokken and one for the application name. The second is optional, but recommended for security reasons. Now go to Settings tab > Secrets:
![github-secrets-menu](img/github-secrets-menu.png)

and create them

![github-secrets-new](img/github-secrets-new.png)



Info: https://dashboard.heroku.com/apps/github-delivery-app-example/deploy/heroku-git

On the terminal, get the heroku app name:
```bash
heroku apps
# === iranzoferri@gmail.com Apps
# github-delivery-app-example (eu) <-- This is app name
```

Set existent git repository:
```bash
heroku git:remote -a github-delivery-app-example
```

Add to the end of .github/workflows/python-app.yml file
```yaml
- name: Deploy to Heroku
  env:
    HEROKU_API_TOKEN: ${{ secrets.HEROKU_API_TOKEN }}
    HEROKU_APP_NAME: ${{ secrets.HEROKU_APP_NAME }}
  if: github.ref == 'refs/heads/main' && job.status == 'success'
  run: |
    git remote add heroku https://heroku:$HEROKU_API_TOKEN@git.heroku.com/$HEROKU_APP_NAME.git
    git push heroku HEAD:main -f
```

Create this two files:
```bash
touch {Procfile,runtime.txt}
```

and put inside the next content...

edit Procfile file:
```bash
web gunicorn --pythonpath src app:app
```

edit runtime.txt file (WARNING, case sensitive):
```bash
python-3.9.0
```
Runtime Heroku help: https://devcenter.heroku.com/articles/python-runtimes

Ok, next is to check if all be ok. Update all changes to your github repository:
```bash
git status
git add .
git commit -m "Launch to Heroku Deployment"
```

To view new app in action follow the provided link:
https://github-delivery-app-example.herokuapp.com/