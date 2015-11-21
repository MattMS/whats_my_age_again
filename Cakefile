fs = require 'fs'

stream = require 'stream'


option '-u', '--url-home [PATH]', 'Base path for URLs (default: "/").'


task 'in', 'Build main page.', (options)->
	if not url_home = options['url-home']
		url_home = '/'

	state = url: home: url_home

	make_page_content state

	.then wrap_in_template

	.then write_index_html

	.then print_done_message

	.catch (err)->
		console.error err


task 'packages', 'Build script of common packages.', (options)->
	package_details = require './package.json'

	dependency_names = (name for name, version of package_details.dependencies)

	console.log dependency_names


make_app_script = (state)->
	# https://stackoverflow.com/questions/12755997/how-to-create-streams-from-string-in-node-js
	code_stream = new stream.Readable()

	state_text = JSON.stringify state

	code_stream.push """
	index = require('./index.coffee.md');
	app = require('./app.coffee.md');
	app(index(#{state_text}))
	"""

	code_stream.push null

	code_stream


make_page_content = (state)->
	new Promise (resolve, reject)->
		try
			browserify = require 'browserify'

			# NOTE: To use this, the CoffeeScript Stream must be converted to JS before adding to browserify.
			#code_stream = fs.createReadStream 'index.coffee.md'

			code_stream = make_app_script state

			browserifying = browserify code_stream
				#extensions: [
					#'.coffee'
					#'.coffee.md'
				#]

			#browserifying.add code_stream

			browserifying.transform 'coffeeify'

			# Make need `require('string_decoder')` for UTF-8 Buffers later.
			# https://nodejs.org/api/string_decoder.html
			browserifying.bundle (err, buf)->
				resolve
					script: buf.toString()
					state: state

		catch err
			reject err


print_done_message = (file_path)->
	console.log "Made #{file_path}"


wrap_in_template = (options)->
	new Promise (resolve, reject)->
		try
			base = require './base'

			result = base options

			#result.childNodes[1].appendChild options.script

			script_tag = "<script>#{options.script}</script>"

			html_text = result.outerHTML.replace '</body>', "#{script_tag}</body>"

			resolve html_text

		catch err
			reject err


write_index_html = (html)->
	new Promise (resolve, reject)->
		file_path = 'index.html'

		fs.writeFile file_path, html, encoding: 'utf8', (err)->
			if err
				reject err
			else
				resolve file_path
