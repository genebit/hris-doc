# h. Custom Utilities for Plugins ❗️

#important

Throughout the application, multiple global utilities are used to reduce redundancy in writing code. These functions are editable under `resources/js/app.js`.

### Global Window Utilities

| Function / Variable                                 | Description                                                                |
| --------------------------------------------------- | -------------------------------------------------------------------------- |
| `window.setNotifPosition(x, y)`                     | Sets the notification position on the screen.                              |
| `window.successNotyf(message)`                      | Shows a success notification with the given message.                       |
| `window.errorNotyf(message)`                        | Shows an error notification with the given message.                        |
| `window.debounce(function, delay)`                  | Creates a debounced version of a function with the given delay.            |
| `window.selectPlaceholder`                          | Placeholder text used for select elements.                                 |
| `window.initSelect2(elements, additional = {})`     | Initializes Select2 on the provided elements with optional configurations. |
| `window.insertEmptyOptionOnSelectData(data)`        | Inserts an empty option into Select2 data.                                 |
| `window.clearSelectOnWireEvent(events, ...selects)` | Clears select2 elements when the specified Livewire events are triggered.  |
| `window.initTooltipMultiSelection(select2)`         | Initializes tooltip functionality for Select2 with multi-selection.        |

Sample usage for `select2`:

```js
$(function() {
	window.initSelect2(['.select__day']);

	handleSelectDay();
});

function handleSelectDay() {
	// -------------------------------------------------------
	// Target the select element to be transformed into Select2
	const $select = $(".select__day");

	Livewire.on('populatedDropdown', function() {
		// Populate the select element with the transformed data
		let data = @this.days.map(function(item) {
			return {
				id: item.day_bits,
				text: item.day_display
			};
		});

		// Add an empty option at the start for the placeholder
		window.insertEmptyOptionOnSelectData(data);

		$select.empty().select2({
			data: data,
			placeholder: window.selectPlaceholder
		});
	});

	// This is when the user selects an option from the dropdown
	$select.on("change", function() {
		@this.set('dayId', $(this).val())
	});

	window.clearSelectOnWireEvent([
		'successProcWorkSchedule',
		'clearTblSelection'
	], $select);

	// Set the values when a row is selected from the table
	Livewire.on('fetchedWorkSchedule', (data) => $select.val(data[0].day_bits).trigger('change'));
}
```

---

➡️ Next up!

[[i. Development Workflow]]