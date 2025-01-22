## Open Source Issue

## Issue I am working on !

Hey all, I have been working on an open source issue where I have to write a python script. The use of python script is to find all the unused and missing json keys.
I have written a code and that is how it goes.

```
import os
import json
import re
import sys


LOCALIZATION_DIR = "./locales"  
CODE_DIRECTORIES = ["./src", "./ui"]  


FILE_EXTENSIONS = [".py", ".js", ".ts", ".vue"] 

def load_localization_keys():
    
    localization_keys = {}
    for filename in os.listdir(LOCALIZATION_DIR):
        if filename.endswith(".json"):
            file_path = os.path.join(LOCALIZATION_DIR, filename)
            with open(file_path, "r", encoding="utf-8") as f:
                try:
                    keys = json.load(f)
                    localization_keys[filename] = keys
                except json.JSONDecodeError as e:
                    print(f"Error decoding JSON in {filename}: {e}")
                    sys.exit(1)
    return localization_keys

def find_keys_in_code():
    
    used_keys = set()
    for root, _, files in os.walk("."):
        for file in files:
            if any(file.endswith(ext) for ext in FILE_EXTENSIONS):
                file_path = os.path.join(root, file)
                with open(file_path, "r", encoding="utf-8") as f:
                    try:
                        content = f.read()
                        # yaha par python regex ka use kiya hai pattern search krne ke liye 
                        matches = re.findall(r"[t\$]\(\s*[\"']([a-zA-Z0-9_.]+)[\"']\s*\)", content)
                        used_keys.update(matches)
                    except Exception as e:
                        print(f"Error reading file {file_path}: {e}")
    return used_keys

def check_unused_and_missing_keys(localization_keys, used_keys):
    
    unused_keys = {}
    missing_keys = set()

    for filename, keys in localization_keys.items():
        file_unused = [key for key in keys if key not in used_keys]
        if file_unused:
            unused_keys[filename] = file_unused

    all_localized_keys = set()
    for keys in localization_keys.values():
        all_localized_keys.update(keys)

    for key in used_keys:
        if key not in all_localized_keys:
            missing_keys.add(key)

    return unused_keys, missing_keys

def main():
    localization_keys = load_localization_keys()
    used_keys = find_keys_in_code()
    unused_keys, missing_keys = check_unused_and_missing_keys(localization_keys, used_keys)

    if unused_keys:
        print("\nUnused localization keys:")
        for filename, keys in unused_keys.items():
            print(f"  {filename}:")
            for key in keys:
                print(f"    - {key}")

    if missing_keys:
        print("\nMissing localization keys:")
        for key in missing_keys:
            print(f"  - {key}")

    if unused_keys or missing_keys:
        sys.exit(1)

    print("No issues found.")

if __name__ == "__main__":
    main()
```

I would love to explain what I am actually trying here.
We are using four libraries - os, json, re and sys.
`os` for navigating or traversing through files, `json` to read and analyse json files, `re` to use regular expression that is to find localization keys in the file and `sys` to exit the program with specific code.

### `def load_localization_keys()`
It loads and reads all the files ending with `.json` in the `LOCALIZATION_DIR` in out case this is `./locales`.
We are using `os` library to list all the json file in ./locales and alsi we are usign join.path function to create a full path of the files we are reading and loading.

### find_keys_in_code()
Same thing is happenign we are just now checking all the files ending with `.py`, `.ts`, `.js` and `.vue`.
This time we are also using regular expressing (regex) to search for different patterns of strings.
And like before we are using `os` library to navigate path and create a full path for the file we want to search.

Rest of the code is easy to understand.
 
