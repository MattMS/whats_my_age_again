# Action centre

Where the plan comes together.


## Find the inputs

	get_input_by_name = (name)->
		document.querySelector "input[name=#{name}]"


## Start it up

We should start with some sort of input so the form isn't too boring.

	window.setup = (selector, initial_name, initial_value)->
		input_names = get_input_names selector

		save_other_names input_names

		add_input_listeners selector

		set_initial_value initial_name, initial_value

		update_other_inputs get_input_by_name initial_name


Helper functions to keep the main setup function clean.

	get_input_names = (selector)->
		for input in document.querySelectorAll selector
			input.getAttribute 'name'

	save_other_names = (input_names)->
		for name, index in input_names
			# Copy the names array.
			other_names = input_names.slice()

			# This returns the removed item so it cannot be chained with `slice`.
			other_names.splice index, 1

			localStorage.setItem "#{name}_other_inputs", other_names.join ','

	set_initial_value = (name, decimal_value)->
		get_input_by_name name
		.value = decimal_value


## Listen for changes

If someone updates one of the inputs then all the other inputs should change to match it.

Since entering inputs will change others, we should listen for the key-up event instead of the change event.

	add_input_listeners = (selector)->
		for input in document.querySelectorAll selector
			input.addEventListener 'keyup', (event)->
				update_other_inputs event.target


## Update inputs

Update the value in the inputs.

	update_other_inputs = (input)->
		name = input.getAttribute 'name'

		radix = input.getAttribute 'data-radix'

		value = parseInt input.value, radix

		for other_input in get_other_inputs name
			if isNaN value
				other_input.value = ''

			else
				other_radix = other_input.getAttribute 'data-radix'

				other_input.value = value.toString other_radix

	get_other_inputs = (name)->
		names = localStorage.getItem "#{name}_other_inputs"
		.split ','

		for other_input_name in names
			get_input_by_name other_input_name

Useful references:

- [parseInt() @ MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseInt)

- [Number.prototype.toString() @ MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toString)
