# Home page

## Imports

	#extend = require 'xtend'

	h = require 'virtual-dom/virtual-hyperscript'

	send = require 'value-event/event'

	#send_change: require 'value-event/change'

	#send_click: require 'value-event/click'

	#send_key: require 'value-event/key'

	#send_submit: require 'value-event/submit'

	#send_value: require 'value-event/value'

	observ = require 'observ'

	observ_struct = require 'observ-struct'


## Exports

	module.exports = (global_state)->
		render: (state)->
			#state = extend global_state, state

			h '.container', [
				h 'h1', "What's my age again?"

				make_form state

				site_footer state
			]

		#state: observ_struct
		state:
			#channels: observ
			channels:
				radix_step: (state, delta)->
					console.log 'radix_step called:', state, delta
					#state.decimal_value.set state.decimal_value + delta

				set_decimal: ({state, value})->
					console.log 'set_decimal called:', state, value
					state.decimal_value.set value

			decimal_value: observ 23


## Form

	make_form = (state)->
		h 'form', [
			#number_input state, 2, 'Binary', #pattern: /^[01]+$/

			number_input state, 8, 'Octal', pattern: /^[0-7]+$/

			number_input state, 10, 'Decimal', pattern: /^[0-9]+$/

			number_input state, 12, 'Dozenal',
				help: 'Duodecimal, if you must.'
				pattern: /^[0-9ABab]+$/

			number_input state, 16, 'Hexadecimal', pattern: /^[0-9A-Fa-f]+$/
		]


### Number buttons

	add_value_button = (state, value)->
		h 'button',
			'ev-click': send state.channels.radix_step, delta: value
		,
			if value > 0
				"+#{value}"
			else
				value.toString()


### Number inputs

	number_input = (state, radix, label, options)->
		input_id = "#{label}_input"

		h '.field', [
			h 'label', htmlFor: input_id, label

			h 'input',
				#'ev-blur': send_value state.channels.set_decimal value: state.decimal_value
				#'ev-event': send_submit
				#'ev-focus':
				#'ev-keydown': send_key
				id: input_id
				name: label
				value: parseInt state.decimal_value, radix

			add_value_button state, -radix

			add_value_button state, radix
		]


## Footer

	site_footer = (state)->
		h 'footer.container', [
			h 'hr'

			h 'nav',
				h 'p', [
					'By '
					a 'MattMS', 'http://mattms.github.io'
				]
		]


## Tags

	a = (label, href)->
		h 'a', href: href, label
