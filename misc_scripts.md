
```dataviewjs
const tripple_tick = "\`\`\`"

// Create code blocks in callouts progrematically
const generate_code_block_in_callout = (plugin, text, indent) => {
	return indent + tripple_tick + plugin + "\n" + text + "\n" + indent + tripple_tick
}

// Create horizontal layout
const gen_multi_column = (plugin_array, text_array, depth) => {
	const indent = ">".repeat(depth);
	const indent_p1 = ">".repeat(depth + 1);
	var out_text = indent + " [!multi-column]"
	for (var i=0; i<plugin_array.length; i++) {
		text = generate_code_block_in_callout(plugin_array[i], text_array[i], indent_p1)
		out_text += "\n" + indent + "\n" + indent_p1 + " [!blank-container|min-0]\n" + text
	}
	return out_text + "\n"
}

const stuff = gen_multi_column(["meta-bind", "meta-bind"], ["INPUT[toggle:test1]", "INPUT[toggle:test2]"], 2)
dv.paragraph(tripple_tick + "\n> [!note]- \n" + stuff + tripple_tick)
```
