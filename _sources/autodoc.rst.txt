Sphinx Autodoc
==============

Sphinx has an autodocumentation extension *autodoc*

To use *autodoc*, you have to activate it in *conf.py* by putting *'sphinx.ext.autodoc'* into the extensions config.

For example, documenting ``io.open()``, reading its signature and docstring from the source file: ::

	.. autofunction:: io.open
	
Document whole classes or even modules automatically, using member options for the auto directives: ::

	.. automodule:: io
		:members:

