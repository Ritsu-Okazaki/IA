###### Ritsu Okazaki IBDP Year 1 Computer Science 1 HL Internal Assessment
# Criteria C
## List of techniques used:
1. Fixed width encoding to store cue data
2. Recursive function for exporting/importing cue data
3. Class for parsing cue data
4. Function for audio trimming and fading
## 1. Fixed width formatting to store cue data
To meet Success Criteria 2, allowing users to edit or add cue information and store them, and Success Criteria 5, allowing users to save or call the cues that they or others have created, I decided to create a standardized storage format in the database. At first, I had the option to create a column for each of the variables in the cue information in the relational database, which would look like below.
|Playback number (overhead)|Strength (overhead)|Transition (overhead)|Pan (floor)|Tilt (floor)|Magnitude (floor)|Red (floor)|Green (floor)|Blue (floor)|Strobe (floor)|Transition (floor)|OnOff (spotlight)|Radius (spotlight)|Focus (spotlight)|Behavior (spotlight)|Transition (spotlight)|File name (audio)|Transition (audio)|
## 2. Recursive function for exporting/importing cue data
To meet Success Criteria 5, allowing users to save or call the cues that they or others have created, I decided to create a method that transfers the cue data that the user has inputted from the database to a exportable file, and vise versa. 
