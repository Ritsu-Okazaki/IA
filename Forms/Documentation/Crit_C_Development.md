###### Ritsu Okazaki IBDP Year 1 Computer Science 1 HL Internal Assessment
# Criteria C
## List of techniques used:
1. Fixed width encoding to store cue data
2. Class for parsing cue data and nested dictionary for fixed width formatting specs
3. Recursive traverse used for exporting cue data with tree
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

## 2. Class for parsing cue data and nested dictionary for fixed width formatting specs
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
The formatting specs `format_spec` comes in a dictionary which contains the tuple of the start digit and the end digit of the value of each component in the string following the fixed width formatting. The dictionaries are stored in another dictionary which contains the specs for different genres, `spec_list`. Again, for readability and scalability, formatting specs of each genre are stored in a dictionary which the function `get_spec` will return the corresponding formatting by the input of the genre.
```.py
def get_spec(column_type:str)->dict:
    spec_list = {"overhead": {"playback": (0,1), "strength": (2,4), "transition": (5,6)},
                 "floor": {"pan": (0,0), "tilt": (1,2), "magnitude": (3,4), "red": (5,6), "green": (7,8), "blue": (9,10), "strobe": (11,11), "transition": (12,13)},
                 "spotlight": {"onoff": (0,0), "radius": (1,1), "focus": (2,2), "behavior": (3,3), "transtion": (4,4)}}
    return spec_list[column_type]
```
In `parse` function, I used dictionary comprehension which iterates through the component and digit tuple in the formatting spec, and formulates and returns a new dictionary by indexing the raw string according to the digits stored in the tuple, as value, and component name for its key.
In `amalgamate` function, the code iterates through the parsed values in the input dictionary `parsed` and adds the value formatted to the correct digit length onto the resultant string which will be returned. To make the values into the length assigned by the formatting spec, I used a method called `rjust(width[, fillchar])` which will add the `fillchar` to the left of the string until the length of the string achieves `width`. In my code, the values which require digit control are all numerical, so the `fillchar` is "0", while the length is the difference between start and end digit stored in the tuple in the given formatting specs.

## 3. Recursive traverse used for exporting cue data with tree
To meet Success Criteria 5, allowing users to save or call the cues that they or others have created, I decided to create a method that transfers the cue data that the user has inputted from the database to a exportable file CSV, and vise versa. However, there is no direct conversion between relational database and CSV. Therefore, to achieve the objective, the program will be required to iterate through the rows in the source, and copy the values of each column to the corresponding row in the target storage. However, each of the cues have hierarchy by having `parent_id`, where a scene can be a child of an act, an act can be a child of a play, and so on. <br>
```
Cue 1: Section
├── Cue 2: Act
│   ├── Cue 4: Scene
│   └── Cue 5: Scene
└── Cue 3: Act
    └── Cue 6: Scene
        └── Cue 7: Sub-scene
```
The program allows unlimited depth, so a for loop cannot be used to iterate through the cues and to find the right order if the child of a cue is queued in an unrelated later row far from the parent cue. To solve this issue, I made functions that uses recursive traversal to obtain cues related in a tree of dynamic depth. <br>
For export, the function allows the input of the file name which will be used for the CSV file that will contain the cues once exported. The function will return only if the process is finished, and will not return any value.
```.py
def export_cue(name:str):
    file = open(f"{name}.csv", "w", newline="")
    database = DatabaseManager('identifier.sqlite')
    all = database.search(query="SELECT * FROM cues ORDER BY parent_id")

    relationship = {}
    cues = {}

    for row in all:
        relationship[row[0]] = []
        cues[row[0]] = row

    for row in all:
        parent_id = row[7]
        if parent_id not in ('', None):
            if parent_id not in relationship:
                relationship[parent_id] = []
            relationship[parent_id].append(row[0])

    order = []

    def traverse(cue_id):
        order.append(cue_id)
        for child in relationship.get(cue_id, []):
            traverse(child)

    for cue_id in sorted(relationship):
        if cues[cue_id][7] == ''):
            traverse(cue_id)

    writer = csv.writer(file)
    for cue_id in order:
        writer.writerow(cues[cue_id])

    file.close()
    database.close()
    return
```
The dictionary `relationship` creates the correspondance between a cue and its children. The function `traverse` will first run on a cue without a parent in `relationship`, then children of the cue are also searched in the same manner if they have children using the `.get` method, and they will sequentially get appended to the list `order` which saves the desired order of the cues. The use of recursive technique, unlike for loops, the program can algorithmically search through the tree structure regardless of the depth, which allows this sorting to be correct no matter what the relationship between the parents and children are at any level.
