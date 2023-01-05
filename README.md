# obsidian-dynamic-table
Dynamic table for Obsidian

# 1. Required Plugins
- Dataview (make sure dataviewjs is enabled)
- Metaedit
- Buttons

# 2. Setup
1. Copy paste everything inside the "START OF DYNAMIC TABLE" and "END OF DYNAMIC TABLE" comments
2. Define your user inputs (see explanations below)
3. Enter and delete a random search (this will create a metadata field required for the table in your note. The table won't render before doing this step)
4. Click the "All" button to initialize the button filter variable. Select sorting options to initialize sorting related vaiarbles (this will write to the inline fields below your table. Feel free to place these anywhere in the note. The table won't render before doing this step)

# 3. User Inputs
The "User inputs" section at the top contains all the required inputs to set up the table. You can select:

## Top Level Filter
This is a tag filter that determines which notes the table will loop over. Current limitations:
- Only one tag currently supported
- Will only work if the tag is the first listed tag in the note (in case the note has more than 1 tags)

## Columns Metadata
This is the metadata you want to display in the table. These are case sensitive, and need to match with the metadata from the notes you are parsing

## Column Titles
These are the titles of the metadata you parsed. Note that the file name is added automatically, so the first column name is currently not modifiable in this top section.

## Buttons
Here you can add buttons. The buttons are second-level filters you use often. They are currently set to work with tags, but in the future more options can be included. Note that no "#" should be included here. The format is as follows:

const buttons = [['button1_name', 'button1_tag'], ['button2_name', 'button2_tag']]


