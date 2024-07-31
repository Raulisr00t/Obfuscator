# C Source Code Obfuscator
This project provides a Python script to obfuscate C source code by removing comments, renaming variables, and removing unnecessary whitespace. The script processes .c and .h files to enhance code privacy and reduce readability, which can be useful for protecting intellectual property or preparing code for competitive programming.

## Features
Comment Removal: Eliminates both single-line (//) and multi-line (/* ... */) comments.
Variable Renaming: Renames variables to random strings, excluding special cases like typedef and unsigned.
Whitespace Removal: Removes unnecessary whitespace while preserving essential formatting.

## Prerequisites
Python 3.x

## Usage
1.Download or clone this repository to your local machine.

2.Navigate to the directory containing the script using your terminal or command prompt.

3.Run the script with the path to your C source code file:
```shell
python obfuscator.py
```

4.Enter the path to your C source code file when prompted.
```shell
Enter a C source code path (include your c file):
```
5.Output: The obfuscated code will be saved in the same directory as the original file, with a prefix obfuscated_.

### Script BreakDown
#### Main Functions
comment_remover(given_string):

Removes single-line and multi-line comments from the given string.
Uses regular expressions to find and delete comments
```python
def comment_remover(given_string):
    given_string = re.sub(r"\/\/.*", "", given_string)
    given_string = re.sub(r"\/\*.*?\*\/", "", given_string, flags=re.DOTALL)
    return given_string
```
variable_renamer(given_string):

Renames variables to random strings, avoiding special cases like typedef and unsigned.
Uses regular expressions to identify variable names and replaces them with randomly generated strings.
```python
def variable_renamer(given_string):
    variable_dictionary = {}
    special_cases = {"typedef", "unsigned"}
    index = 0
    new_string = ""

    split_code = re.split('\"', given_string)
    filtered_code = re.findall(
        r"(?:\w+\s+)(?!main)(?:\*)*([a-zA-Z_][a-zA-Z0-9_]*)", given_string)

    for found_example in filtered_code:
        if found_example not in special_cases:
            if found_example not in variable_dictionary:
                variable_dictionary[found_example] = random_string(12)

    for section in split_code:
        if index % 2 == 0:
            for entry in variable_dictionary:
                re_string = r"\b{}\b".format(entry)
                section = re.sub(re_string, variable_dictionary[entry], section)

        if index >= 1:
            new_string = new_string + "\"" + section
        else:
            new_string = new_string + section
        index += 1

    return new_string
```
whitespace_remover(a):

Removes unnecessary whitespace while preserving essential formatting.
Uses regular expressions to identify and preserve critical code elements.
```python
def whitespace_remover(a):
    splits = re.split('\"', a)
    code_string = r"(\w+\s+[a-zA-Z_*][a-zA-Z0-9_]*|#.*|return\s+[a-zA-Z0-9_]*|else)"
    index = 0
    a = ""

    for s in splits:
        if index % 2 == 0:
            s_spaceless = re.sub(r"\s+", "", s)
            s_code = re.findall(code_string, s)

            for code in s_code:
                old = re.sub(r"\s+", "", code)
                new = code
                if code.startswith("#"):
                    new = code + "\n"
                elif "unsigned" in code or "else" in code:
                    new = code + " "
                s_spaceless = s_spaceless.replace(old, new)
        else:
            s_spaceless = s

        if index >= 1:
            a = a + "\"" + s_spaceless
        else:
            a = a + s_spaceless
        index += 1

    return a
```
random_string(stringLength=8):

Generates a random string of lowercase letters of a specified length.
```python
def random_string(stringLength=8):
    letters = string.ascii_lowercase
    return ''.join(random.choice(letters) for i in range(stringLength))
```
## Main Script
The main script reads the file, processes it through the above functions, and saves the obfuscated code:
```python
def main():
    file_path = input("Enter a C source code path (include your c file):")
    if not os.path.exists(file_path):
        print("Please check your source code path!\n")
        return

    print(f"Looking for C Source Files in {file_path}...")

    filename = os.path.basename(file_path)
    cwd = os.path.dirname(file_path)

    if filename.endswith(".c") or filename.endswith(".h"):
        with open(file_path, 'r') as file_data:
            file_string = file_data.read()
            print("PASS\n")
            file_string = comment_remover(file_string)
            file_string = variable_renamer(file_string)
            file_string = whitespace_remover(file_string)
            obfuscated_filename = os.path.join(cwd, "obfuscated_" + filename)
            with open(obfuscated_filename, "w+") as f:
                f.write(file_string)
            print(file_string)
    else:
        print("FAIL")

if __name__ == "__main__":
    main()
```
## License
This project is licensed under the MIT License.
