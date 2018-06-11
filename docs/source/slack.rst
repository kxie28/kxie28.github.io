Slack Test App
==============

``virtual env`` is a tool to create isolated Python environments. 
The basic problem being addressed is one of dependencies and versions, and indirectly permissions. Imagine you have an application that needs version 1 of LibFoo, but another application requires version 2. How can you use both these applications? If you install everything into ``/usr/lib/python2.7/site-packages`` (or whatever your platform’s standard location is), it’s easy to end up in a situation where you unintentionally upgrade an application that shouldn’t be upgraded.

So, first create the python virtual environment::

	virtualenv env

To activate the environment::

	source env/scripts/activate

On Windows::

	env\Scripts\activate

To install the local webserver tunnel::

	npm install -g localtunnel

Create the accessible localtunnel::

	lt --port 8080


This is a test app::

	import webapp2
	import random
	import os
	import re
	from slackclient import SlackClient
	token = "xoxp-..."
	sc = SlackClient(token)
	class Home(webapp2.RequestHandler):
		def get(self):
			self.response.write("Test")

	class GetCoffee(webapp2.RequestHandler):
		def post(self):
			beverage = self.request.get('text')
			get_beverage(beverage,self)

	def list_channels():
		channels_call = sc.api_call("channels.list")
		if channels_call['ok']:
			return channels_call['channels']
		return None

	def channel_info(channel_id):
		channel_info = sc.api_call("channels.info", channel=channel_id)
		if channel_info:
			return channel_info['channel']
		return None

	def get_beverage(beverage, self):
		if beverage == "coffee":
			print sc.api_call(
				"chat.postMessage", channel="#general", text="COFFEE! :tada:"
			)
		elif beverage == "water":
			print sc.api_call(
				"chat.postMessage", channel="#general", text="Time to get WATER :tada:"
			)
		elif beverage == "tinkles":
			print sc.api_call(
				"chat.postMessage", channel="#general", text="GO TINKLES TOM :poop:"
			)
		else:
			print sc.api_call(
				"chat.postMessage", channel="#general", text="Not a valid input. Try Again. :mask:"
			)

	app = webapp2.WSGIApplication([
					(r'/', Home),
					(r'/coffee', GetCoffee)
					],
					debug=True)

	def main():
		from paste import httpserver
		httpserver.serve(app, host='127.0.0.1', port='8080')

	if __name__ == '__main__':
		main()

