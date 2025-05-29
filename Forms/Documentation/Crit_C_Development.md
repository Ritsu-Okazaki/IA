###### Ritsu Okazaki IBDP Year 1 Computer Science 1 HL Internal Assessment
# Criteria C
## List of techniques used:
1. Fixed width encoding to store cue data
2. Recursive function for exporting/importing cue data
3. Class for parsing cue data
4. Function for audio trimming and fading
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


## 2. Recursive function for exporting/importing cue data
To meet Success Criteria 5, allowing users to save or call the cues that they or others have created, I decided to create a method that transfers the cue data that the user has inputted from the database to a exportable file, and vise versa. 
