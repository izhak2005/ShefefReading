name: Build Installer

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: windows-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.9'
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        
    - name: Download Inno Setup
      run: |
        Invoke-WebRequest "https://files.jrsoftware.org/is/6/innosetup-6.2.1.exe" -OutFile "innosetup.exe"
        Start-Process -FilePath "innosetup.exe" -ArgumentList "/VERYSILENT /SUPPRESSMSGBOXES /NORESTART /SP-" -Wait
    
    - name: Build installer
      run: |
        & 'C:\Program Files (x86)\Inno Setup 6\ISCC.exe' setup.iss
    
    - name: Upload artifact
      uses
