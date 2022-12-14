
name: Tests

on: 
  push:
    paths:
      - '.github/workflows/tests.yml'
      - 'deepface/**'  
      - 'tests/**'
      - 'api/**'
      - 'requirements.txt'
      - '.gitignore'
      - 'setup.py'
  pull_request:
    paths:
      - '.github/workflows/tests.yml'
      - 'deepface/**'  
      - 'tests/**'
      - 'api/**'
      - 'requirements.txt'
      - '.gitignore'
      - 'setup.py'

jobs:
  lint_with_flake8:

    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: [3.8]

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v2
      with:
        python-version: ${{ matrix.python-version }}
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install flake8
    - name: Lint with flake8
      run: |
        # stop the build if there are Python syntax errors
        flake8 . --count --select=E9,F63,F7 --show-source --statistics
        # exit-zero treats all errors as warnings. The GitHub editor is 127 chars wide
        flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
  unit-tests-ubuntu-latest:
    needs: lint_with_flake8

    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: [3.8]

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v2
      with:
        python-version: ${{ matrix.python-version }}
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install pytest
        pip install .
        
    - name: Test with pytest
      run: |
        cd tests
        pytest unit_tests.py
    
