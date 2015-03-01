# Useful code snippets

## Turn on DB logging on a single file

import logging
l = logging.getLogger('django.db.backends')
l.setLevel(logging.DEBUG)
l.addHandler(logging.StreamHandler())

## Using cProfile inline and sending output to file (useful for celery!)
	import cProfile, pstats, StringIO
	pr = cProfile.Profile()
	pr.enable()
	# Do Stuff
	foo.bar()
	pr.disable()
	s = StringIO.StringIO()
	sortby = 'cumulative'
	ps = pstats.Stats(pr, stream=s).sort_stats(sortby)
	ps.print_stats()
	f = open('/Users/james/Documents/pookie.txt', 'w+')
	f.write(s.getvalue())