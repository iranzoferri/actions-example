# Command list

Create a virtual environment:
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
