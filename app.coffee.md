# Mercury app

## Imports

	delegator = require 'dom-delegator'

	document = require 'global/document'

	extend = require 'xtend'

	main = require 'main-loop'

	observ = require 'observ'

	observ_struct = require 'observ-struct'


## Exports

	module.exports = ({render, state})->
		state_copy = extend state

		state_copy.channels = observ null

		state_copy_struct = observ_struct state_copy

		handles = {}

		for name, channel of state.channels
			channel.bind null, state_copy_struct

			handles[name] = delegator.allocateHandle channel

		state_copy_struct.channels.set handles

		delegator()

		main_loop = main state_copy_struct(), render,
			create: require 'virtual-dom/vdom/create-element'
			diff: require 'virtual-dom/vtree/diff'
			patch: require 'virtual-dom/vdom/patch'

		document.body.appendChild main_loop.target

		state_copy_struct main_loop.update
