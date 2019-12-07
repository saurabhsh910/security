# Python Fundamentals

## Array/list <a id="arraylist"></a>

```text
my_list = [1,"string",3,4,5]
for item in my_list:
    print item

# Append/push to list
my_list.append("addMe")
```

## Modules <a id="modules"></a>

Always good to modular your code.

**module1.py**

```text

def addNumbers(numberOne, numberTwo):
    return numberOne + numberTwo
```

**script.py**

```text
import module1

total = module1.addNumbers(1,2)
print total
```

## Pip - package management <a id="pip---package-management"></a>

Pip is the python package manager. It ca be used to download other modules.

Install pip

```text
sudo apt-get install python-pip
```

To install package

```text
pip install package
```

