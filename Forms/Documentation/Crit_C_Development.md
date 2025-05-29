###### Ritsu Okazaki IBDP Year 1 Computer Science 1 HL Internal Assessment
# Criteria C
## List of techniques used:
1. Fixed width encoding to store cue data
2. Class for parsing cue data
3. Recursive function for exporting/importing cue data
5. Function for audio trimming and fading
## 1. Fixed width formatting to store cue data
To meet Success Criteria 2, allowing users to edit or add cue information and store them, and Success Criteria 5, allowing users to save or call the cues that they or others have created, I decided to create a standardized storage format in the database. At first, I had the option to create a column for each of the variables in the cue information in the relational database, which would look like below. <br>
![image](https://github.com/user-attachments/assets/62045649-8169-4a21-a757-8f91d93fe980) <br>
_Fig. Relational database with column per variable_ <br>
I realized that, though this method does not require parsing, having 18 columns obfuscates the program, especially because variables of same genre (e.g. overhead, floor) are not bundled. I thought that the indexing of the 18 columns will delay the development and debugging process. A possible solution for this issue was to devide the table into multiple sub tables bundled by the same genre, based on database design concept like below. <br>
![image](https://github.com/user-attachments/assets/c610e02d-a8ab-41ae-a124-06a7a597e4f1) <br>
_Fig. Relational database with table per genre, column per variable_ <br>
However, I realized that when the user decides to edit or add a cue, transfer are required in multiple tables, which risks the consistency of data and adds the process for iterating through the tables, as well as increasing the steps required to merge data again in the export of the data in Success Criteria 5. <br>
After the discovery, I concluded that fixed width formatting per genre is the most favorable option to avoid obfuscation, process redundancy and maintaining data consistency. This method is suitable for the situation especially because the length of the each variable is small and most has clear maximum and minimum, which makes it easy for me to decide the fixed width. Before coding the database, I created my own grammer sheet that annotates which digits represent which variable in the string for each genre. This had set a fundamental standard for data management in the product, and helped me during the development process.
```
QMan Fixed width formatting notations

overhead 
AABBBCC (7 characters)
[AA: playback. 1~40]
[BBB: strength. 0~100]
[C: transition time to next. 0~99 (0 will show instantaneous)]

floor
ABBCCDDEEFFGHH (16 characters)
[A: pan. "L" for left, "M" for middle, "R" for right]
[B: tilt. 0~10]
[C: magnitude. 0~10]
[D: Red. 0~10]
[E: Green. 0~10]
[F: Blue. 0~10]
[G: Strobe. "F" for fast, "M" for middle, "S" for slow]
[H: transition time to next. 0~99 (0 will show instantaneous)]

spotlight
ABCDE (5 characters)
[A: on/off. 0 for off, 1 for on]
[B: spot radius. "B" for big, "M" for medium, "S" for small]
[C: spot focus. "S" for sharp, "M" for medium, "V" for vague]
[D: behavior. "F" for follow, "L" for left, "M" for middle, "R" for right]
[E: transition time to next. 0~99 (0 will show instantaneous)]

audio
A---B (undefined characters)
[A: audio file name with the extension]
[B: transition time to next. 0~99 (0 will show instantaneous)]
```

## 2. Class for parsing cue data
To meet Success Criteria 1, allowing users to navigate through cue information that they have added, a parser that will decode the fixed width formatting in the database was required for the variables to be usable for display in the UI. It is also necessary for the program to amalgamate the user UI input, into the fixed width formatting style. To do this, one option was to create functions for each formatting specs that has the input of the raw value, for both parsing and amalgamating. However, having considered the principle of modular programming, I discovered that this is unideal, as there will be multiple functions of similar method yet different condition which are not standardized. This obfuscates the code and hinders the readability as well as increasing the difficulty to maintain consistency. <br>
Therefore, I decided to create a class `MultipleParser` for generalizing the process, which contains the functions to parse and amalgamate. The object will be initialized by the input of the formatting specs in `format_spec`. A pair of functions of two inputs, formatting specs and raw value, for both parsing and amalgamating would have served the same purpose, but I chose to use a class as there were no clear difference in performance, and I believe the usage of class would have better readability and pursue more of the principles of modular programming.
```.py
class MultipleParser:
    def __init__(self, format_spec:dict):
        self.format_spec = format_spec

    def parse(self, raw:str)->dict:
        return {
            name: raw[start:end+1]
            for name, (start, end) in zip(self.format_spec.keys(), self.format_spec.values())
        }

    def amalgamate(self, parsed:dict)->str:
        result = ""
        for p in parsed:
            print(parsed[p])
            length = self.format_spec[p][1] - self.format_spec[p][0]
            result += str(parsed[p].rjust(length, "0"))
        return result
```

## 3. Recursive function for exporting/importing cue data
To meet Success Criteria 5, allowing users to save or call the cues that they or others have created, I decided to create a method that transfers the cue data that the user has inputted from the database to a exportable file CSV, and vise versa. However, there is no direct conversion between relational database and CSV. Therefore, to achieve the objective, the program will be required to iterate through the rows in the source, and copy the values of each column to the corresponding row in the target storage. One possible option is to use a loop in the main code, but to simplify, I created a dedicated function for each of import and export. I made functions that are self-contained which does not require on any global variables, based on the concept of modular programming. <br>
For export, the function allows the input of the file name which will be used for the CSV file that will contain the cues once exported. The function will return only if the process is finished, and will not return any value.
```.py
def export_cue(n=1, database=None, file=None, name=None):
    if n == 1:
        file = open(f"{name}.csv", "w", newline="")
        database = DatabaseManager('identifier.sqlite')
    row = database.search(query = f"SELECT * FROM cues WHERE cue_id = {n}")
    csv.writer(file).writerow(row[0])
    print(row[0])
    has_next = database.search(query = f"SELECT exists(SELECT 1 FROM cues WHERE cue_id = {n+1}) AS row_exists")
    if has_next[0][0] == 1:
        export_cue(n+1, database, file)
    else:
        database.close()
        return
```



