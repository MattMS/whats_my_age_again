# Base template

## Imports

	h = require 'hyperscript'


## Exports

Ideally we could add the `script` from the arguments as a tag, but Hyperscript encodes the content, so all ampersands become `&amp;`.

	module.exports = ({state})->
		h 'html', lang: 'en',
			h 'head', [
				h 'meta', charset: 'utf-8'

				h 'title', "What's my age again?"

				css get_absolute_url state, '/components/main.css'
			]

			h 'body',
				h 'noscript', 'You need Javascript enabled to view this page.'


## Tags

	a = (label, href)->
		h 'a', href: href, label


	css = (href)->
		h 'link',
			href: href
			rel: 'stylesheet'
			type: 'text/css'


	script = (url)->
		h 'script',
			src: url
			type: 'text/javascript'


## URL

	get_absolute_url = (state, url)->
		if not home_url = state?.url?.home
			home_url = ''

		if home_url = '/'
			home_url = ''

		"#{home_url}#{url}"
