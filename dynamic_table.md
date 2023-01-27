%% ======================================================================START OF DYNAMIC TABLE====================================================================== %%

> [!Example]- Table Options
> # Custom Search 
> ```meta-bind
> INPUT[text:internal_search]
> ```
> # Data Formatting
>  Include To-do status in table:
> ```meta-bind
> INPUT[toggle:include_todo]
> ```
> Only show meetings with incomplete tasks:
> ```meta-bind
> INPUT[toggle:show_incomplete_only]
> ```
>  List incomplete tasks:
> ```meta-bind
> INPUT[toggle:list_todos]
>  ```

```dataviewjs
// ================================================================================== USER INPUTS ===============================================================================

// Tag filter, the table will loop through all notes with this tag (include hashtag)
const top_level_filter = "#people"

// Metadata to include in the table
const columns_metadata = ["tag"] //for notes of format <YYYY-MM-DD - note_name>, adding file.day will render the date in the column

// Titles of the columns for the above listed metadata
const columns_titles =  ["Author Category"]

// Pull author categories
var category_buttons = []
var categories_log = []

const categories_data = dv.pages(top_level_filter)
	.forEach(p => {
		if ((p.file.tags[0].includes('/'))) {
			//pull category
			var tag_id = p.file.tags[0]
			var category_temp = p.file.tags[0].substring(8).replace("_", " ").replace("_", " ").replace("_", " ")
			var category = category_temp[0].toUpperCase() + category_temp.substring(1)
			
			//Add category if it doesn't already exist, then add quote as first element
			if (!categories_log.includes(category)){
				categories_log.push(category)
				category_buttons.push([category, tag_id.substring(1)])
			}
		}
	})

// ================================================================== BUILD BUTTONS =================================================================
const {update} = this.app.plugins.plugins["metaedit"].api
const {createButton} = app.plugins.plugins["buttons"]
const file = dv.current().file.path
dv.paragraph('')

// "All" button
createButton({app, el: this.container, args: {name: "All"}, clickOverride: {click: update, params: ['filter', top_level_filter.slice(1), file]}});

// Custom buttons
for (let i = 0; i < category_buttons.length; i++){
	createButton({app, el: this.container, args: {name: category_buttons[i][0]}, clickOverride: {click: update, params: ['filter', category_buttons[i][1], file]}});
}

// =================================================================================================================================================================================
dv.paragraph("*Active filters: *" + dv.current().internal_search + ", " + dv.current().filter)
dv.paragraph('')

// ======================================================================================TABLE======================================================================================

// Define variables
var filter = dv.current().filter
const other = dv.current().other
var table_data = []
const internal_search = dv.current().internal_search.toLowerCase()
var todo_contents = []

// Function to extract text content from a file
async function extract_content(file){
	const content = await dv.io.load(file.file.path)
	return content.toLowerCase()
}

//Loop through all notes
for (const p of dv.pages(top_level_filter)) {
		if (p.file.tags[0].match(dv.current().filter) && (p.file.name.includes(internal_search) || (await extract_content(p)).includes(internal_search))) {
			let file_data = []
			file_data.push("[[" + p.file.name + "]]")

			// loop through desired columns and add metadata value to file data
			for(let i = 0; i < columns_metadata.length; i ++) {
				file_data.push(p[columns_metadata[i]])
			}

			// Check if todos are complete or not. Add to table depending on if you only want incomplete tasks to show
			if (p.file.tasks.completed.includes(false)) {
				if(dv.current().include_todo){
					file_data.push("❌")
				}
				table_data.push(file_data)
			} else {
				if(dv.current().include_todo){
					file_data.push("✔️")
				}
				if (!dv.current().show_incomplete_only) {
					table_data.push(file_data)
				}
			}

			// Parse note for tasks
			if (dv.current().list_todos && p.file.tasks.completed.includes(false)) { // if note contains incomplete todos
				let note_todos = []
				for (let i = 0; i < p.file.tasks.length; i++) { // loop over all tasks in note
						if (!p.file.tasks[i].completed){ // if task is incomplete						
						note_todos.push(p.file.tasks[i].text) // add to intermediate list (so we can attach all incomplete tasks to the same note)
						
					} // task complete check if-statement close
				} // task parse for loop cose
			
			todo_contents.push([p.file.name, note_todos])
			} // task parse if-statement close		
			
			
		} // filter match if-statement close (everything above this is repeated for each note)	
	} // top level for loop close

// Add file name and todo status (if desired)
columns_titles.unshift('File')
if(dv.current().include_todo){
	columns_titles.push("Status")
}

// Sort data based on user inputs
const sort_index = columns_titles.indexOf(dv.current().sort_name)

if (dv.current().sort_order == "Ascending"){
		table_data.sort((a, b) => b[sort_index] - a[sort_index])
		
	} else if (dv.current().sort_order == "Descending"){
		table_data.sort((a, b) => a[sort_index] - b[sort_index])
	
	} else {
	dv.paragraph("Invalid sort settings")
}

// Build Table
dv.table(columns_titles, table_data.reverse())

// Render buttons for sorting options
dv.paragraph("Sort by:")
for (let i = 0; i < columns_titles.length; i++){
	createButton({app, el: this.container, args: {name: columns_titles[i]}, clickOverride: {click: update, params: ['sort_name', columns_titles[i], file]}});
}
dv.paragraph('')

// Render buttons for sorting order
createButton({app, el: this.container, args: {name: "Ascending"}, clickOverride: {click: update, params: ['sort_order', "Ascending", file]}});
createButton({app, el: this.container, args: {name: "Descending"}, clickOverride: {click: update, params: ['sort_order', "Descending", file]}});

// Render content of unchecked todo's if desired
if (dv.current().list_todos) {
	dv.paragraph("## Incomplete Tasks")
	for (let i = 0; i < todo_contents.length; i++) {
		dv.paragraph("### Incomplete tasks in [[" + todo_contents[i][0] + "]]:")
		dv.paragraph(todo_contents[i][1])
	}
}
```
##### Inline Fields
filter:: people
sort_name:: Effective cashflow [€]
sort_order:: Descending
%% ======================================================================END OF DYNAMIC TABLE====================================================================== %%
